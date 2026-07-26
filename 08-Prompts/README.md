---
tags: [meta, prompts, ai]
updated: 2026-07-26
---

# 08 — Prompts

Prompts probados que funcionan. Si tuve que escribir el mismo prompt dos veces,
va acá con las mejoras de la segunda.

**Diferencia con `06-Skills/`:** una skill es un procedimiento que el agente ejecuta.
Un prompt es texto que yo pego.

---

## Escritos

- [[Contenido - articulo original desde una fuente]] — corriendo en producción a diario.

---

## Convención de nombres

`<área> - <objetivo>.md`

```
Review - buscar bugs en un diff.md
SEO - auditar metadata de una página.md
Refactor - extraer lógica a service.md
Contenido - descripción de producto en ES.md
```

---

## Plantilla

```markdown
---
tags: [prompt, <área>]
updated: YYYY-MM-DD
model: opus | sonnet | gemini | cualquiera
---

# <Objetivo>

## Cuándo lo uso
Situación concreta.

## Prompt
```
<el prompt exacto, con {{placeholders}} para lo que cambia>
```

## Qué devuelve bien
Lo que sí resuelve.

## Limitaciones
Dónde falla. Qué hay que revisar a mano después.

## Variantes
Ajustes que probé y qué pasó.
```

---

## Lo que hace que un prompt mío funcione

1. **Rol y contexto primero.** Stack, proyecto, restricciones.
2. **Objetivo en una frase**, arriba, no enterrado.
3. **Formato de salida explícito.** Si no lo pido, viene ensayo.
4. **Criterio de "listo".** Qué cuenta como buena respuesta.
5. **Pedir crítica, no validación.** "Encontrá los problemas" > "¿está bien?".
6. **Enlazar al vault.** "Seguí mis [[Coding Principles]]" ahorra media página de instrucciones.
7. **Un ejemplo** de la salida esperada vale más que tres párrafos de descripción.

---

## Enlaces

- [[Code Review]] · `06-Skills/`
