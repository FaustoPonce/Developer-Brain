---
tags: [meta, skills, ai]
updated: 2026-07-26
---

# 06 — Skills

Capacidades reutilizables que le doy a los agentes de IA. Una skill es un
procedimiento empaquetado: se invoca por nombre y el agente sabe exactamente qué hacer.

**Cuándo creo una:** hice el mismo procedimiento 3 veces explicándoselo a la IA desde cero.

---

## Qué NO va acá

- Prompts sueltos de una sola vez → `08-Prompts/`
- Listas para tildar a mano → `09-Checklists/`
- Conocimiento sin procedimiento → `02` a `04`

---

## Qué hay hoy acá

| | Qué es |
|---|---|
| [[CATALOG]] | Índice de las **2049 skills** que tengo bajadas en `OBJETIVOS/antigravity skills/`. Mapa, no copia. |
| [[06-Skills/imported/README\|imported/]] | Las **50** que elegí porque tocan mis proyectos. Copiadas sin adaptar. |
| *(por escribir)* | Las mías, en la plantilla de abajo. Todavía ninguna. |

**La distinción importa:** lo de `imported/` es material ajeno sin verificar. Una skill
mía es la que escribí yo después de hacer el procedimiento tres veces. No mezclar:
las mías van en la raíz de `06-Skills/`, las importadas se quedan en su carpeta.

---

## Candidatas propias

- `pre-deploy-check` — correr la checklist de [[Deployment]]
- `review-diff` — review contra [[Coding Principles]]
- `new-landing` — scaffold con la estructura de [[Folder Structure]]

Para SEO probablemente no necesite escribir una: ver primero si `seo-audit` o
`nextjs-seo-indexing` de `imported/` ya lo resuelven.

---

## Plantilla

```markdown
---
name: nombre-en-kebab-case
description: Cuándo usar esta skill. Una línea, específica — de esto depende que el agente la elija.
tags: [skill]
updated: YYYY-MM-DD
---

# <Nombre>

## Cuándo se usa
Disparadores concretos. Y cuándo NO usarla.

## Entradas
Qué necesita para arrancar (ruta del repo, URL, archivo).

## Pasos
1. ...
2. ...

## Criterio de éxito
Cómo sé que terminó bien.

## Referencias
[[nota del vault]]
```
