---
tags: [meta, checklists, process]
updated: 2026-07-26
---

# 09 — Checklists

Listas mecánicas. No se piensa, se tilda.

Existen porque bajo presión (deploy con algo roto, feature apurada) es cuando más
me olvido de lo obvio. Una checklist convierte conocimiento en acción garantizada.

---

## Reglas de una buena checklist

1. **Cada ítem es verificable.** "Revisar SEO" no sirve. "Canonical absoluta y única en cada página" sí.
2. **Corta.** Más de 15 ítems y dejo de usarla. Si crece, se parte en dos.
3. **En orden de ejecución**, no por importancia.
4. **Cada ítem existe por una razón real** — idealmente una nota de `07-Lessons Learned/`.
5. **Se poda.** Ítem que nunca falla en 20 usos, se saca.

---

## Escritas

- [[SEO Launch]] — antes de que Google vea el sitio. Cada ítem salió de un error real.

## A escribir

- `Pre-Deploy.md` — antes de tocar producción. Ver [[Deployment]]
- `Post-Deploy.md` — los 5 minutos después
- `New Project Setup.md` — versión tildable de [[New Project]]
- `Security Review.md` — [[Authentication]] + [[Validation]]
- `Accessibility.md` — mínimo aceptable de a11y
- `Performance.md` — Core Web Vitals antes de lanzar

---

## Plantilla

```markdown
---
tags: [checklist, <área>]
updated: YYYY-MM-DD
---

# <Nombre>

**Cuándo:** momento exacto en que se corre.
**Tiempo:** ~X minutos.

## Bloqueantes
- [ ] ...

## Importantes
- [ ] ...

## Deseables
- [ ] ...

---
**Origen de los ítems:** [[lección que lo agregó]]
```

---

## Enlaces

- [[Deployment]] · [[New Project]] · [[Code Review]]
