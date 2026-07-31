---
tags: [patterns, seo, geo, ia, crawlers]
updated: 2026-07-31
status: active
---

# AI Crawler Discoverability (llms.txt)

> Un archivo de texto plano pensado para que asistentes de IA (ChatGPT, Perplexity,
> Claude, etc.) entiendan de qué trata el sitio sin tener que rastrear cientos de
> páginas, y puedan citarlo/recomendarlo con contexto correcto.

---

## Qué NO es

**No es un mecanismo de acceso ni de indexación.** No reemplaza `robots.txt` ni el
sitemap, y no "activa" que las IAs puedan crawlear el sitio — eso ya lo decide
`robots.txt`. Un `userAgent: '*'` con `allow: '/'` ya permite a todos los bots,
incluidos `GPTBot`, `ClaudeBot`, `PerplexityBot`, sin configuración extra.

`llms.txt` es puramente un **atajo de contexto**: en vez de que el crawler infiera
la estructura del sitio leyendo HTML, se la doy servida y resumida.

Ver [[2026-07-31 - robots wildcard ya cubre a los crawlers de IA]].

---

## Los dos archivos

### `llms.txt` (corto, en `/public/llms.txt`)

Resumen denso, pensado para caber en poco contexto:

- Una línea de qué es el sitio (`> descripción`).
- Páginas principales con link y descripción de una línea.
- Patrón de URL de las páginas generadas (`/en/games/{slug}`, etc.).
- Notas del modelo de datos si hay algo que el crawler podría malinterpretar
  (ver sección de veracidad más abajo).
- Idiomas soportados.

### `llms-full.txt` (largo, en `/public/llms-full.txt`)

Mismo contenido pero expandido: cómo funciona el sitio paso a paso, tabla de
categorías/tiendas/idiomas, cómo se monetiza, y detalle técnico (stack, JSON-LD,
sitemap). Es el que uso si el asistente de IA necesita precisión en vez de
velocidad.

---

## La misma regla de veracidad que el resto del SEO programático

Si el sitio ya tiene un contrato de "sin dato real, no se afirma" (ver
[[Programmatic SEO]] y [[2026-07-26 - Datos fabricados en schema y metadatos]]),
`llms.txt` tiene que respetar el mismo contrato — es lo que un asistente de IA va a
citar textualmente al usuario.

Ejemplo real (Crossplay Checker): la nota de datos en `llms.txt` dice explícitamente
que el sitio **nunca afirma qué plataformas específicas cruzan entre sí** más allá de
lo verificado, porque la fuente es inconsistente. Si esa aclaración no estuviera, un
asistente de IA podría inventarle una respuesta específica y falsa a un usuario en mi
nombre — el error se propaga fuera del sitio, sin que yo lo vea.

---

## Checklist para replicarlo en otro proyecto

- [ ] `llms.txt`: qué es, páginas principales, patrón de URL de páginas generadas,
      notas de honestidad de datos si aplica, idiomas.
- [ ] `llms-full.txt`: lo mismo expandido + monetización + stack técnico.
- [ ] Confirmar que `robots.txt` ya permite `*` — si no, esto no sirve de nada.
- [ ] No poner nada en estos archivos que no esté también verificado en el sitio real
      (mismo principio que metadatos y schema).

---

## Enlaces

[[Programmatic SEO]] · [[SEO Launch]] · [[2026-07-31 - robots wildcard ya cubre a los crawlers de IA]]
