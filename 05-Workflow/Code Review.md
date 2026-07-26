---
tags: [workflow, review, quality, ai]
updated: 2026-07-26
status: draft
---

# Code Review

> Trabajo solo: mis reviewers son mi yo de mañana y la IA. Igual reviso.

---

## Mi propio código (auto-review)

Antes de mergear, leer el diff completo **como si fuera de otra persona**.
`git diff main...HEAD`. Suena a ceremonia; encuentra bugs.

Ayuda: dejarlo dormir. Reviso al día siguiente y veo cosas que ayer no.

---

## Checklist de review

### Correctitud
- [ ] ¿Hace lo que dice el commit/PR?
- [ ] Casos borde: array vacío, `null`, string vacío, número 0, negativos
- [ ] Async: ¿falta algún `await`? ¿hay race conditions?
- [ ] ¿Errores manejados o silenciados? Ver [[Error Handling]]

### Seguridad
- [ ] Input externo validado. Ver [[Validation]]
- [ ] Ownership verificado en endpoints con `:id`. Ver [[Authentication]]
- [ ] Sin secrets en el diff
- [ ] Sin datos sensibles en logs o en el bundle del cliente
- [ ] SQL parametrizado

### Diseño
- [ ] ¿Está en la capa correcta? Ver [[Clean Architecture]]
- [ ] ¿Abstracción prematura? Regla de tres
- [ ] ¿Hay algo duplicado que ya existe en el repo?
- [ ] Nombres claros. Ver [[Naming]]

### Frontend
- [ ] Los 4 estados: loading, empty, error, success. Ver [[Components]]
- [ ] Accesible: semántica, labels, foco, contraste
- [ ] Móvil probado
- [ ] Sin layout shift

### Higiene
- [ ] Sin `console.log` de debug ni código comentado
- [ ] Sin `any` ni `@ts-ignore` sin justificar
- [ ] Sin `TODO` sin contexto

---

## Review con IA

Sirve mucho, con el prompt correcto. Lo que funciona:

- **Dar contexto primero**: qué hace el código, qué me preocupa, qué stack.
- **Pedir crítica, no aprobación**: "encontrá los bugs" > "¿está bien?".
- **Preguntas específicas**: "¿hay race condition acá?", "¿qué pasa si el array viene vacío?".
- **Pedirle que revise contra este vault**: "revisá esto contra mis [[Coding Principles]]".

Lo que no funciona: pegar 500 líneas sin contexto y esperar algo útil.

**La IA no reemplaza el criterio.** Acepta el cambio solo si entiendo por qué.
Código que no entiendo no entra a `main`.

---

## Prioridad de los hallazgos

| Nivel | Qué es | Acción |
|---|---|---|
| **Bloqueante** | Bug, agujero de seguridad, pérdida de datos | Se arregla ahora |
| **Importante** | Mal diseño que va a doler pronto | Se arregla antes de mergear |
| **Menor** | Naming, estilo, preferencia | Se arregla si es rápido |
| **Nota** | Idea para después | A la lista, no al PR |

No bloquear un merge por gustos personales — ni siquiera los míos.

---

## Enlaces

- [[Git]] · [[Coding Principles]] · [[Debugging]] · `08-Prompts/`
