---
tags: [lesson, seo, search-console, checklist]
updated: 2026-08-21
severity: bajo
---

# "Página con redirección" en Search Console nunca valida si el redirect es a propósito

## Síntoma

Mail automático de Search Console: "No se han podido corregir problemas de
Indexación de páginas" — resultado de validación: **error**, sobre el motivo
"Página con redirección". El conteo de páginas afectadas había subido (no bajado)
desde la última revisión.

## Impacto

Ninguno real — el susto es el costo. Sin verificación, esto lee como una
regresión real (¿rompí algo de nuevo?) y dispara una sesión entera de debugging
sobre un sitio que está sano.

## Causa raíz

Hay dos motivos completamente distintos que Search Console agrupa bajo la misma
etiqueta "Página con redirección":

1. **El propio sitio emitía la URL rota** (sitemap, `<a>` planas, JSON-LD armado a
   mano apuntando a una versión sin `/` final, sin HTTPS, etc.) — esto **sí** se
   arregla, y una vez arreglado dejan de aparecer *URLs nuevas* en esa categoría.
2. **La URL redirige a propósito, para siempre** (`http://` → `https://`, sin `/`
   → con `/`, `www.` → raíz, la raíz `/` → `/idioma/`). Google la sigue
   encontrando por enlaces externos viejos, cachés propios o simple curiosidad del
   crawler, y **confirma correctamente que sigue redirigiendo** — porque eso es
   lo que tiene que pasar, para siempre. No hay nada que "corregir" ahí: el
   redirect ES la corrección.

El botón "Validar corrección" no distingue entre los dos casos. Para el motivo 2,
la validación automática de Google **nunca puede pasar**, porque la condición que
chequea (¿la URL reportada sigue redirigiendo?) va a ser verdadera por diseño,
para siempre. Un aumento en el conteo tampoco es señal de nada roto: es Google
re-descubriendo más variantes viejas (enlaces externos, caché) del mismo patrón
ya sano.

## Por qué no lo vi antes

El mail de Google usa el mismo lenguaje de alarma ("no se han podido corregir")
para los dos casos, y el conteo subiendo activa la misma sospecha que un bug real.
No hay forma de distinguirlos desde el mail — hay que abrir los ejemplos reales y
seguir la cadena de redirects a mano.

## Fix

No hay nada que arreglar en el sitio cuando el patrón es el 2. La verificación
real: `curl -sL -o /dev/null -w "%{num_redirects} %{http_code} %{url_effective}"`
sobre cada URL de ejemplo del reporte — si termina en **un solo salto** (dos para
la raíz `/`, que encadena protocolo + idioma) y **200** en la versión canónica,
está sano. Si un ejemplo termina en **404** después del redirect, ahí sí vale la
pena investigar — pero antes de asumir que es una regresión propia, **verificar
si esa URL exacta alguna vez existió como slug real** (grep contra los datos del
proyecto): en un caso real, la URL reportada por Google tenía una palabra faltante
en el slug (`type-c-3-0` en vez de `type-c-usb-3-0`) — nunca fue una URL del
sitio, era una referencia externa mal formada (backlink roto, scraper, etc.), y el
producto real respondía 200 en su slug correcto.

## Cómo lo prevengo

- No usar el botón "Validar corrección" en Search Console para un motivo de
  redirect que es permanente por diseño (protocolo, trailing slash, www, raíz de
  idioma) — va a fallar siempre, sin importar qué tan sano esté el sitio. Sirve
  solo para el caso 1 (URL que el propio sitio emitía y ya se corrigió).
- Nuevo hecho en `AGENTS.md` (sección SEO): un aumento en "Página con redirección"
  no es señal de regresión sin verificar primero los ejemplos reales con `curl -L`.
- Si un ejemplo específico 404ea después del redirect, cruzarlo contra los datos
  reales del proyecto antes de tratarlo como bug propio — puede ser una URL que
  nunca existió.

## Enlaces

[[SEO Launch]] · [[2026-08-16 - trailingSlash no se propaga fuera de Link]]
