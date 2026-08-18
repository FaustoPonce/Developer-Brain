---
tags: [lesson, seo, pages-template, contenido, datos]
updated: 2026-08-11
severity: medio
---

# Datos de ejemplo del template publicados como productos reales

## Síntoma

Un producto llamado literalmente "Ejemplo — Dock Completo 100W" (marca "Ejemplo")
aparecía en la página real de compatibilidad de Steam Deck de CompatCheck, indexado
como si fuera un producto de verdad — con badge de "confirmado" y todo. El usuario lo
encontró navegando el sitio en vivo, no yo auditando.

## Impacto

Bajo en este caso puntual (1-2 productos ficticios entre 890 reales, encontrados antes
de la revisión de AdSense), pero es exactamente el tipo de hallazgo que un revisor
humano de AdSense sí nota, y que erosiona la confianza en cualquier otro dato del sitio
si aparece.

## Causa raíz

`Pages-Template` (la base de la que se clonan todos los proyectos del portfolio) trae
3 entradas de ejemplo en `items.json` a propósito, para servir de referencia de los
casos límite del schema (completo/incompleto/un solo idioma). El propio `README.md`
del template decía textualmente **"Se borran cuando haya datos reales"** — una
instrucción para un humano, en un archivo que nadie vuelve a abrir una vez que el
proyecto tiene tracción real. Con 890 productos cargados a lo largo de 13 sesiones de
research, nadie volvió a ese README ni a esas 3 líneas de `items.json`. Dos de las tres
entradas tenían `confirmedDevices` de ejemplo (steam-deck, rog-ally, legion-go), así que
pasaban la puerta de calidad del sitio (`specsSource` + ≥1 dispositivo confirmado) igual
que cualquier producto real, y se servían sin ninguna señal de que eran ficticias.

## Por qué no lo vi antes

Una nota en un README es una intención, no una barrera. Nada en el pipeline de build
o en `verify-seo.mjs` distingue "producto de ejemplo del scaffold" de "producto real" —
para el código son indistinguibles, ambos tienen `specsSource` y `confirmedDevices`.
El bug no se ve auditando datos nuevos (la atención siempre está en el research recién
cargado); se ve navegando el sitio como un visitante real, algo que no se hace seguido
una vez que el research se vuelve rutina.

## Fix

- Borrar las 3 entradas de `items.json` en cuanto el catálogo tiene datos reales
  (no esperar a "cuando tenga tiempo") y borrar la sección del README que las describe,
  para que no quede una referencia fantasma a algo que ya no existe.

## Cómo lo prevengo

- **Agregar un chequeo real, no solo una nota.** Si `Pages-Template` se sigue usando
  para proyectos nuevos, vale la pena que `verify-seo.mjs` (o el `npm run check`
  equivalente) falle si detecta `brand === 'Ejemplo'` (o el marcador que sea) en el
  build de producción — convertir la intención del README en una barrera automática,
  no un recordatorio que depende de que alguien lo lea meses después.
- Como parte de cualquier checklist de pre-lanzamiento (ver [[SEO Launch]]), agregar
  el ítem explícito "sin datos de ejemplo del scaffold en el dataset de producción" —
  no asumir que "ya lo borré" sin grep.
- Aplica a cualquier proyecto clonado de `Pages-Template` con un dataset propio
  (`items.json` o equivalente), no solo a CompatCheck.

## Enlaces

[[SEO Launch]] · [[2026-07-26 - Datos fabricados en schema y metadatos]] ·
[[Programmatic SEO]]
