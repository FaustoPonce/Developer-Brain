---
tags: [lesson, seo, i18n, indexacion]
updated: 2026-07-26
severity: alto
---

# Rutas localizadas generaron ~800 URLs duplicadas

## Síntoma

Google crawleaba cientos de URLs y se negaba a indexarlas. En Search Console aparecían
como "rastreada, actualmente sin indexar" y "duplicada". El sitio parecía tener el doble
de páginas de las que tenía.

## Impacto

~800 URLs basura en un solo sitio. Crawl budget quemado en páginas que nunca iban a
rankear, y la señal de calidad del dominio por el piso.

## Causa raíz

Con rutas traducidas (`/en/game/x` y `/es/juego/x`) y un segmento padre `[lang]`,
**el generador de rutas hace el producto cartesiano**: si la ruta hija solo declara el
slug y hereda el idioma del padre, se generan también `/en/juego/x` y `/es/game/x`.

Esas dos combinaciones no existen conceptualmente, pero existen como archivos, devuelven
200 y Google las encuentra.

## Por qué no lo vi antes

Asumí que declarar la ruta `juego` dentro de la carpeta del idioma alcanzaba para
atarla al español. No alcanza: la carpeta define la *forma* de la URL, no restringe
qué valores toma el segmento del idioma.

Nada falla. No hay error de build, no hay 404, no hay warning. Las páginas se ven bien
si las abrís. El único lugar donde el problema aparece es Search Console, semanas después.

## Fix

**Cada ruta localizada fija su propio idioma al generar los parámetros estáticos.**
La ruta en español emite solo entradas en español; la inglesa, solo en inglés.

Segunda capa de defensa, independiente de la primera: **la canonical se deriva de la
identidad del contenido, no de la URL por la que se llegó**. Si un artículo es español,
su canonical apunta a la URL española, sin importar bajo qué prefijo se lo pidió. Así,
aunque se cuele una combinación inválida, no compite.

## Cómo lo prevengo

- **Verificación automática después del build**: comparar las URLs del sitemap con las
  páginas realmente generadas. Sobra algo ⇒ producto cartesiano. Falta algo ⇒ el sitemap
  anuncia 404. Está implementado en `scripts/verify-seo.mjs` de la plantilla
  (`Template proyectos/Pages-Template`), atado a `npm run check`.
- Regla fija: **toda ruta declara explícitamente en qué idiomas existe.** No es
  "fijar un idioma": es decidirlo. Ver la tabla de los dos casos en
  [[Internationalization]] — un path idéntico en ambos idiomas necesita lo contrario
  que un path traducido, y aplicarle la regla por reflejo lo rompe.
- Regla fija: **canonical derivada del contenido**, nunca del parámetro de la URL.

## Estado en mis proyectos

- Sitio de Steam Deck: **corregido**, con el motivo escrito en el propio código.
- Radar de ofertas: **correcto desde el arranque**.
- Sitio de FUT: **sigue vulnerable**. Las rutas de noticias no fijan el idioma; hoy lo
  salva la canonical derivada del contenido, pero las URLs basura se siguen generando.

## Enlaces

[[Internationalization]] · [[NextJS]] · [[SEO Launch]]
