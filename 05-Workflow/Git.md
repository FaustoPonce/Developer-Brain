---
tags: [workflow, git, process]
updated: 2026-07-26
status: draft
---

# Git

> Trabajo solo, pero mi historial lo lee mi yo del futuro. Y los agentes de IA.

---

## Branching

Trabajo solo, así que uso lo mínimo que funciona:

- `main` — siempre deployable.
- `feat/nombre-corto` — una feature.
- `fix/nombre-corto` — un bug.

Branch de vida corta: máximo 2-3 días. Merge y borrar. Ramas largas = conflictos garantizados.

Cambios triviales (typo, texto, config) van directo a `main`.

---

## Commits

Conventional Commits. Formato: `tipo(scope): descripción en imperativo`.

| Tipo | Para |
|---|---|
| `feat` | Funcionalidad nueva |
| `fix` | Bug |
| `refactor` | Cambio interno sin cambiar comportamiento |
| `perf` | Performance |
| `style` | Formato, sin efecto funcional |
| `docs` | Documentación |
| `test` | Tests |
| `chore` | Deps, config, build |

```
feat(games): agregar filtro por plataforma
fix(auth): validar ownership en GET /orders/:id
chore(deps): actualizar next a 15.2
```

**Reglas:**
- Imperativo y en minúscula: "agregar", no "agregado" ni "Agrega".
- Una intención por commit. Si el mensaje lleva "y", son dos commits.
- Sin `wip`, `cambios`, `asdf`, `fix bug` en `main`.
- El **cuerpo** explica el *por qué* si no es obvio. El diff ya dice el *qué*.

---

## Reglas duras

1. **Nunca commitear secrets.** Si pasa: rotar la clave **y** limpiar la historia. Cambiar el archivo no alcanza — queda en el historial.
2. **`.gitignore` desde el commit 1**: `.env*`, `node_modules`, `.next`, `dist`, `*.local`.
3. **Nada de `--force` sobre `main`.** `--force-with-lease` sobre mi propia rama, y solo si sé por qué.
4. **Lockfile commiteado.** Siempre.
5. **Revisar el diff antes de commitear.** `git diff --staged`. Ahí aparecen los `console.log`.
6. **No commitear código comentado.** Para eso está el historial.

---

## Antes de cargar datos: chequear que el working tree esté limpio de verdad

No asumir que el repo está limpio solo porque la memoria/sesión anterior dijo "todo commiteado" — puede haber cambiado desde entonces, y puede haber **otra sesión de un agente de IA corriendo en paralelo sobre el mismo repo** sin que yo lo sepa (otro chat de Claude Code abierto al mismo tiempo, por ejemplo).

Antes de empezar a escribir/cargar datos en cualquier tarea (no solo research masivo): `git status` + `git diff --stat` + `git fetch origin`.

Si aparece trabajo sin commitear que no reconozco:
1. No descartarlo ni pisarlo a ciegas — puede ser real.
2. Verificarlo con spot-checks contra el estado actual (¿los datos citan fuentes reales? ¿coinciden con lo que dice contener?).
3. Si es real, fusionar el propio trabajo encima (ej. un loader que salta silenciosamente lo que ya existe) en vez de sobreescribir.

**Por qué:** pasó de verdad en CompatCheck (2026-08-11) — una sesión arrancó a cargar datos asumiendo working tree limpio y recién al escribir descubrió ~400 líneas sin commitear de otra sesión paralela, con trabajo real y verificable. Se fusionó sin perder nada, pero podría haberse pisado si no se hubiera chequeado antes de escribir.

---

## Comandos que uso seguido

```bash
git status -sb                        # estado compacto
git diff --staged                     # revisar antes de commitear
git add -p                            # commits quirúrgicos
git log --oneline --graph -20         # ver qué pasó
git commit --amend                    # arreglar el último (solo si NO está pusheado)
git restore --staged <file>           # sacar del stage
git restore <file>                    # descartar cambios (destructivo)
git stash push -m "wip login"         # guardar para después
git bisect start                      # cuándo se rompió
git reflog                            # red de seguridad: nada se pierde de verdad
```

---

## Cuando algo sale mal

| Situación | Solución |
|---|---|
| Commit con mensaje malo (no pusheado) | `git commit --amend` |
| Commit en la branch equivocada | `git cherry-pick` al destino, `git reset --hard` en la origen |
| Deshacer último commit, conservar cambios | `git reset --soft HEAD~1` |
| Revertir algo ya pusheado a `main` | `git revert <sha>` — nunca reescribir historia pública |
| "Perdí todo" | `git reflog`. Casi nunca se perdió de verdad. |

---

## Enlaces

- [[Code Review]] · [[Deployment]] · [[Coding Principles]]
