---
tags: [identity, decisions, process]
updated: 2026-07-26
status: draft
---

# Decision Making

> Cómo decido, para no re-decidir lo mismo cada vez.

---

## Filtro rápido (30 segundos)

Antes de meterme en una decisión técnica, tres preguntas:

1. **¿Es reversible?** Si sí → decido rápido y sigo. Si no → me tomo el tiempo.
2. **¿Me bloquea hoy?** Si no me bloquea, la difiero y anoto el trade-off.
3. **¿Ya lo decidí antes?** Buscar en el vault. Si está escrito, se respeta.

---

## Puertas de una vía vs de dos vías

| Tipo | Ejemplos | Cómo lo trato |
|---|---|---|
| **Dos vías** (reversible) | Librería de UI, formato de fecha, estructura de carpeta | Decidir en minutos. Peor caso: refactor. |
| **Una vía** (cara de revertir) | Base de datos, auth provider, modelo de datos, gateway de pagos, dominio | Escribir un ADR. Dormirla una noche. |

---

## Criterios de elección de tecnología

En orden de peso:

1. **¿Ya lo sé usar?** Aprender cuesta semanas que no tengo.
2. **¿Tiene comunidad?** Si el error no está en Google/Stack Overflow, me como el debug entero.
3. **¿Cuánto cuesta a escala 0?** Empiezo con free tier o no empiezo.
4. **¿Puedo salir?** Vendor lock-in solo si el ahorro es enorme.
5. **¿Está mantenido?** Último commit, issues abiertas, releases.
6. **Hype.** Último criterio, y con peso negativo.

Ver [[Development Philosophy]] → "Lo aburrido gana".

---

## Cuándo construir vs usar librería

**Uso librería si:** es un problema resuelto (fechas, auth, validación, pagos, i18n).
**Construyo si:** es lógica de mi dominio, o la librería trae 10x lo que necesito.

Nunca escribo a mano: criptografía, parsing de fechas, manejo de zonas horarias, sanitización de HTML.

---

## Template de ADR

Para decisiones de una vía. Guardar en `07-Lessons Learned/` o en `docs/adr/` del proyecto.

```markdown
# ADR-000: <decisión>

**Fecha:** YYYY-MM-DD
**Estado:** propuesto | aceptado | reemplazado por ADR-XXX

## Contexto
Qué situación me obliga a decidir. Restricciones reales (plata, tiempo, skill).

## Opciones
1. **A** — pro / contra
2. **B** — pro / contra

## Decisión
Elegí X.

## Por qué
La razón concreta, no la genérica.

## Consecuencias
Qué se vuelve fácil. Qué se vuelve difícil. Qué queda como deuda.

## Cuándo revisar esto
Señal concreta que me haría reconsiderar (ej: "si supero 10k usuarios").
```

---

## Anti-patrones míos (para vigilar)

- **Rabbit hole de research.** Timebox: 45 min. Después decido con lo que tengo.
- **Refactor de escape.** Refactorizar para no enfrentar el feature difícil.
- **Optimización sin medición.** Ver [[Development Philosophy]].
- **Sobre-generalizar el primer caso.** Ver [[Coding Principles]] → regla de tres.

---

## Enlaces

- [[Development Philosophy]]
- [[Coding Principles]]
- [[New Project]]
