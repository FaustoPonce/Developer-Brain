---
tags: [lesson, seo, internacionalizacion, fuente-primaria]
updated: 2026-08-14
severity: bajo
---

# Selector de idioma en la raíz no es antipatrón de Google

## Síntoma

El vault tenía anotado como antipatrón "página raíz que solo elige idioma" con el
argumento de que desperdicia la URL más fuerte del dominio. Se lo recomendé a un
proyecto real como si fuera una corrección de SEO necesaria.

## Impacto

Bajo, pero por poco: casi se aplica un cambio de arquitectura basado en una regla
que no existe, presentada con la misma confianza que un hallazgo verificado.

## Causa raíz

La regla nunca se contrastó contra la fuente primaria. Es un heurístico que circula
en la comunidad SEO (server root = URL de más autoridad, no desperdiciarla en una
pantalla vacía), pero la documentación de Google dice lo contrario en el punto
específico: **`x-default` fue diseñado explícitamente para páginas selectoras de
idioma** — es el caso de uso que la etiqueta tiene pensado, no un error a corregir.

Tampoco es cierto que la prioridad del sitemap en esa URL sea una "señal fuerte
desperdiciada": Google trata `priority`/`changefreq` como señales que
mayormente ignora.

## Por qué no lo vi antes

La regla sonaba razonable y estaba redactada con la misma seguridad que el resto del
vault, así que se propagó de nota en nota sin que nadie la contrastara. El filtro de
"¿esto sirve en 6 meses?" evalúa si algo es duradero, pero no evalúa si es *cierto* —
puede sonar duradero y estar mal igual.

## Fix

Antes de escribir una regla de SEO como bloqueante, contrastarla contra la
documentación de Google Search Central si existe una página oficial sobre el tema
puntual (multilenguaje, hreflang, sitemaps, structured data). Si la fuente primaria
contradice el heurístico de comunidad, gana la fuente primaria.

Lo que sí queda en pie, con el peso correcto: un selector de idioma en la raíz sí
tiene un costo real, pero es de **UX** (un clic extra para tráfico directo/de marca)
y de **equity de enlaces** (si alguien linkea al dominio pelado, ese valor no fluye a
la versión con contenido) — no un error de indexación ni una regla de Google.
Saltear ese clic con un 301 a la versión por defecto puede seguir siendo la decisión
correcta, pero por esas dos razones, no por la que estaba escrita.

## Cómo lo prevengo

- Las notas de `04-Patterns/` que citan "esto es lo que hace Google" sin enlazar la
  fuente son sospechosas por default — se verifican antes de repetirlas.
- Separar explícitamente, en cualquier recomendación de SEO: qué es regla documentada
  de Google, qué es heurístico de comunidad, y qué es UX/negocio disfrazado de SEO.

## Enlaces

[[Programmatic SEO]] · [[SEO Launch]] · [[Internationalization]]
