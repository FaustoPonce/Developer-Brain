---
tags: [lesson, debugging, data, tooling]
updated: 2026-08-16
severity: medio
---

# Fallo uniforme en un chequeo automático es el chequeo, no el dato

## Síntoma

Un script de Node escrito para auditar ~300 candidatos de producto (extraídos de 7
informes de research en markdown) contra un catálogo JSON de 1078 productos reportó
que **el 100% de las referencias a productos existentes citadas por la research
("este producto ya existe con id:NNN") apuntaban a ids inexistentes** — decenas de
casos, sin ninguna excepción.

## Impacto

De haber confiado en el resultado tal cual, la conclusión habría sido "toda la
research de las 7 tandas cita ids fabricados" — una acusación grave de fabricación
masiva contra siete auditorías independientes que, en todo lo demás, venían siendo
consistentes y bien fundamentadas. Habría bloqueado la carga completa por desconfianza
infundada.

## Causa raíz

El catálogo guardaba `id` como **string** (`"6"`, `"817"`), pero el script de
verificación construía el índice de búsqueda y comparaba con el `id` extraído del
texto ya convertido a **number** (`Number(idMatch[1])`). `Map.has(6)` contra una clave
`"6"` (string) siempre da `false` — el bug afectaba a absolutamente todas las
comparaciones por igual, no a un subconjunto.

```js
// ❌ silencioso: byId nunca matchea, cero excepciones
const byId = new Map(items.map(i => [i.id, i]));
byId.has(Number(idMatch[1]))

// ✅
const byId = new Map(items.map(i => [String(i.id), i]));
byId.has(idMatch[1])
```

## Por qué no lo vi antes

El script se escribió asumiendo el tipo de `id` sin verificarlo — una suposición
razonable (la mayoría de catálogos usan number) que en este caso era incorrecta. La
señal de alarma real no fue "hay errores", fue **la uniformidad**: cuando 81 chequeos
independientes sobre datos con contenido completamente distinto fallan todos de la
misma forma exacta, eso no es una propiedad de los datos (que son heterogéneos por
naturaleza) — es una propiedad del chequeo. Un dato real fabricado falla de forma
irregular; un bug de comparación falla siempre.

## Fix

`typeof items[0].id` antes de escribir cualquier comparación, no después de que el
resultado sorprenda. Costó un `node -e` de una línea una vez sospechado.

## Cómo lo prevengo

- **Regla nueva para `Static Data as Database`**: antes de escribir un script de
  verificación cruzada contra el JSON, confirmar el tipo real de los campos clave
  (`typeof item.id`) — no asumirlo por convención de otros proyectos.
- **Heurística de debugging transversal, no específica de este bug**: si un chequeo
  automático reporta la misma clase de fallo en el 100% de los casos, sospechar
  primero del chequeo (comparación, tipo, parsing) antes que del dato — la
  uniformidad total es la pista, no la cantidad de fallos.
- Este mismo patrón (parsear ~300 candidatos vía regex desde markdown en vez de
  transcribir a mano, cruzar contra el catálogo + entre tandas) escaló bien una vez
  corregido: encontró 1 duplicado real que el propio informe de research no detectó,
  2 productos propuestos de forma independiente por dos tandas distintas, y validó
  ~300 referencias en segundos en vez de una por una a mano.

## Enlaces

[[Static Data as Database]] · [[Debugging]] · [[Content Pipelines]]
