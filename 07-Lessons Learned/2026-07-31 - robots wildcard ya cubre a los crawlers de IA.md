---
tags: [lesson, seo, geo, ia, crawlers, robots]
updated: 2026-07-31
severity: bajo
---

# robots wildcard ya cubre a los crawlers de IA

## Síntoma

Preocupación de que los crawlers de IA (GPTBot, ClaudeBot, PerplexityBot) no tuvieran
acceso al sitio porque todavía no existía `llms.txt`.

## Causa raíz

Confusión entre dos cosas distintas:

1. **Acceso** — lo decide `robots.txt`. Un `userAgent: '*'` con `allow: '/'` ya
   permite a *todos* los bots, sin necesidad de listarlos uno por uno.
2. **Contexto/discoverability** — lo ayuda `llms.txt`. Es un resumen del sitio para
   que el crawler entienda la estructura más rápido, no un permiso.

Faltaba solo lo segundo. Lo primero ya estaba resuelto desde que existe el
`robots.txt` con wildcard — no había ningún bloqueo real.

## Por qué no lo vi antes

Dije la frase "todavía no configuramos eso" refiriéndome a `llms.txt`, y sonó (sin
querer) a que el acceso estaba bloqueado. Generó alarma innecesaria del lado del
usuario, que razonablemente entendió "no indexa en las IAs" en vez de "falta un
archivo de contexto opcional".

## Fix

Ninguno técnico — el sitio ya estaba bien. El fix fue de comunicación: verificar
`robots.ts` en vivo antes de afirmar nada sobre acceso de crawlers, y separar
explícitamente "acceso" de "discoverability" al explicarlo.

## Cómo lo prevengo

- Antes de decir que "falta" algo relacionado a crawlers, releer `robots.txt`/`robots.ts`
  del proyecto puntual — no asumir por memoria de otro proyecto.
- Al hablar de `llms.txt`, aclarar siempre que es un atajo de contexto, no un gate de
  acceso. Ver [[AI Crawler Discoverability]].

## Enlaces

[[AI Crawler Discoverability]] · [[Programmatic SEO]]
