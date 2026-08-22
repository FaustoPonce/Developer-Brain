---
tags: [lesson, seo, sitemap]
updated: 2026-08-22
severity: medio
---

# `lastmod` dinámico hace que Google deje de confiar en el sitemap

## Síntoma

Google ignora el `lastmod` del sitemap y decide por su cuenta cuándo re-crawlear.
Las páginas que sí cambiaron no se re-indexan más rápido que las que no.

## Impacto

Se pierde la única señal directa que tengo para decirle a Google "esto cambió,
volvé a mirarlo". En un sitio con miles de URLs, eso es control real sobre el
crawl budget.

## Causa raíz

Generar el `lastmod` con la fecha actual en tiempo de build. Cada deploy —aunque solo
haya tocado un color— le dice a Google que **todas** las páginas cambiaron.

Google detecta rápido que la señal no se corresponde con cambios reales y la descarta
para todo el dominio.

## Por qué no lo vi antes

Parece lo correcto: "el sitemap debe reflejar cuándo se generó". Pero `lastmod` no
significa "cuándo se generó el archivo", significa **cuándo cambió el contenido de
esa URL**. Son dos cosas distintas y solo una le sirve a Google.

## Fix

- Si el dato existe por entidad (fecha de actualización del registro), usar ese,
  por URL.
- Si no existe, una constante que se sube **a mano** cuando cambia contenido real.
  Es menos elegante y funciona mejor que la fecha automática.
- Nunca la fecha del build.

## Cómo lo prevengo

Ítem en la checklist de SEO: buscar la fecha dinámica en el generador del sitemap.
Si aparece, está mal. El test automatizado tiene que inspeccionar esa fuente de verdad;
no debe fallar solo porque una fecha manual válida coincide con el día del deploy.

## Estado en mis proyectos

- Radar de ofertas y sitio de Steam Deck: **corregidos**, con constante manual.
- Sitio de crossplay: **sigue con fecha de build** en todas las entradas. Es el
  mismo error, sin corregir, en un sitio con ~1400 URLs.

Vale la pena anotarlo: la lección se aprendió en un proyecto y no se llevó a los otros.
Ver [[Development Philosophy]] — por eso existe este vault.

## Enlaces

[[Programmatic SEO]] · [[SEO Launch]] · [[NextJS]]
