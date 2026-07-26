---
tags: [lesson, seo, contenido, adsense, monetizacion]
updated: 2026-07-26
severity: alto
---

# Plantillas fijas de contenido = thin content y rechazo de AdSense

## Síntoma

Cientos de páginas rastreadas y **no indexadas**. AdSense rechazado por "contenido de
poco valor". Tráfico orgánico plano pese a tener muchas URLs publicadas.

## Impacto

Dos rechazos de AdSense en sitios distintos y miles de páginas fuera del índice.
El sitio existía, pero para Google no aportaba nada.

## Causa raíz

Las páginas se generaban desde una plantilla fija: mismo párrafo introductorio, mismas
4 preguntas frecuentes, misma estructura, cambiando solo el nombre de la entidad.

Escalar así produce **muchas URLs, un solo contenido**. Google no cuenta páginas, cuenta
información distinta. Mil páginas con la misma plantilla valen lo que una.

## Por qué no lo vi antes

Confundí *cantidad de páginas* con *cantidad de contenido*. El razonamiento era
"más páginas = más SEO = más ads", y con SEO programático eso es falso salvo que cada
página aporte algo propio.

También ayudó que las páginas se ven bien de a una. El problema solo es visible
comparando dos páginas cualesquiera lado a lado.

## Fix

Tres cosas, en orden de impacto:

1. **Variación determinística.** Cada página elige su variante de texto a partir de un
   hash estable de su identificador. Mismo id ⇒ mismo texto siempre (estable entre
   builds, sin desajuste de hidratación), pero distinto del resto.
2. **Inyectar datos reales de la entidad** en la prosa: año, género, puntuación, precio,
   plataformas. Eso es lo que hace la página genuinamente distinta, no las variantes.
3. **Puerta de calidad antes de publicar:** solo entran al sitemap las páginas que ya
   tienen contenido propio. Lo que todavía es plantilla existe pero no se ofrece a Google.

## Cómo lo prevengo

- **Regla:** antes de generar N páginas, responder qué tiene la página 500 que no
  tenga la 499. Si la respuesta es "el nombre", no se genera.
- El sitemap se filtra por calidad, no por existencia. Ver [[Programmatic SEO]].
- Test manual barato: abrir dos páginas al azar del mismo tipo y leerlas en paralelo.

## Nota

La variación determinística por hash la implementé dos veces en proyectos distintos
antes de darme cuenta de que era un patrón. Ya está escrita en [[Programmatic SEO]].

## Enlaces

[[Programmatic SEO]] · [[Content Pipelines]] · [[SEO Launch]]
