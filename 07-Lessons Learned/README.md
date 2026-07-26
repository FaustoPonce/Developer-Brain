---
tags: [meta, lessons, postmortem]
updated: 2026-07-26
---

# 07 — Lessons Learned

Errores que ya cometí. La carpeta más valiosa del vault a largo plazo:
todo lo que hay acá me costó horas o plata.

**Cuándo escribo una nota:** un bug me robó más de una hora, o algo se rompió en producción,
o una decisión salió mal.

---

## No depende de que me acuerde

Dos piezas, fuera del vault, para que capturar no dependa de la memoria:

| | Qué hace |
|---|---|
| **Hook** `~/.claude/hooks/brain-reminder.sh` | Después de un `git commit -m` de tipo `fix`, `perf` o `refactor`, avisa. Son los commits que vienen justo después de resolver algo. No dispara en `feat`, `docs` ni `chore`: un recordatorio que aparece siempre se vuelve invisible. |
| **Comando** `/brain` | Captura la lección aplicando las 4 reglas del vault: filtra por los 6 meses, decide en qué carpeta va, busca si ya está escrito, y cierra el círculo (checklist, plantilla, contradicciones). |

Se administran con `/hooks`. El hook está en la config global, así que aplica en todos
los proyectos.

Sin culpa y sin adornos. El objetivo no es castigarme, es no repetirlo.

---

## Convención de nombres

`YYYY-MM-DD - descripción corta.md`

```
2026-07-23 - Canonical relativa duplicó 631 páginas.md
2026-06-10 - Deploy sin env var tiró prod 40 minutos.md
```

La fecha ordena y da contexto de qué sabía yo en ese momento.

---

## Plantilla

```markdown
---
tags: [lesson, <proyecto>, <tema>]
updated: YYYY-MM-DD
severity: bajo | medio | alto
---

# <Qué pasó>

## Síntoma
Qué vi. Lo observable, no la causa.

## Impacto
Tiempo perdido, usuarios afectados, plata. Concreto.

## Causa raíz
La causa de verdad, no la primera explicación plausible.

## Por qué no lo vi antes
Qué asumí. Qué señal ignoré. Esta sección es la que más enseña.

## Fix
Qué hice para resolverlo.

## Cómo lo prevengo
Test de regresión, ítem en una checklist, cambio de proceso.
Si no genera una acción concreta, la lección no está aprendida.

## Enlaces
[[nota del vault que actualicé por esto]]
```

---

## Regla de oro

Toda lección debe **modificar algo**: una nota de este vault, una checklist en
`09-Checklists/`, o un test. Si no cambia nada, la voy a repetir.
