---
tags: [lesson, seo, nextjs, routing, checklist]
updated: 2026-08-22
severity: alto
---

# `trailingSlash: true` no se propaga fuera de `<Link>`

## Síntoma

Search Console: cientos de páginas de producto marcadas "Página con redirección",
detectadas ~5 días después de migrar el hosting a Cloudflare Workers Static Assets
(`trailingSlash: true` ya estaba en `next.config.js` desde antes de esa migración).

## Impacto

El propio `sitemap.ts` apuntaba a **todo** el catálogo (miles de URLs) sin `/` final,
mientras el hosting exige la barra y devuelve 307 a quien pide la versión sin ella —
cada URL del sitemap le costaba a Google un salto de redirect antes de llegar al
contenido real. Mismo bug en `llms.txt` y en el `url`/`item` de todo el JSON-LD
(`BreadcrumbList`, `ItemList`) de las páginas generadas. Y en el header/footer del
sitio entero (logo, nav, links legales) el bug vivía en etiquetas `<a>` planas, no
`<Link>` — ahí no es solo un problema de rastreo, es un 307 real en cada click de
cada visitante, en cada página del sitio.

## Causa raíz

`trailingSlash: true` solo le dice a Next.js **cómo servir** cada ruta (`/ruta/index.html`
accesible en `/ruta/`). No reescribe ningún string que el código arme a mano.

`<Link>` de `next/link` sí normaliza el trailing slash al resolver el `href` — pero
solo cuando el href interpola una ruta **100% dinámica** (`[lang]`, `[lang]/[landing]`,
`[lang]/product/[slug]`): la resolución de rutas dinámicas reconstruye el path desde
el patrón de la ruta, y ahí aplica la config. Una ruta estática mixta (`[lang]/faq`:
un segmento dinámico + uno literal) **no** se normaliza — `<Link>` pasa el string tal
cual. Y cualquier cosa fuera de `<Link>` (`<a>` planas, `sitemap.ts`, `llms.ts`,
JSON-LD embebido a mano) nunca pasa por esa normalización, sin importar si la ruta de
destino es estática o dinámica.

## Por qué no lo vi antes

La verificación de "trailing slash consistente" que sí se hizo al migrar el hosting
fue manual: 2-3 URLs navegadas a mano en el browser. Navegar a mano solo prueba los
`<Link>` que YA se auto-corrigen — nunca las `<a>` planas ni las URLs que solo un
crawler visita (sitemap, llms.txt, JSON-LD). El chequeo automático de SEO del
proyecto (`verify-seo.mjs`, corre en cada build) valida canonical/hreflang/lastmod/
duplicados, pero no verifica que cada URL emitida en el sitemap responda 200 en vez
de 3xx — ese hueco específico no lo cubría ninguna herramienta automática, solo
Search Console, con días de latencia real de detección.

## Fix

Un helper explícito al lado de las funciones de ruteo existentes (`pathFor`/
`dynamicPathFor` → `pathForSlash`/`dynamicPathForSlash`), usado en todo lo que **no**
sea un `<Link>`: `sitemap.ts`, `llms.ts`, JSON-LD, y cualquier `<a>` plana. No se
tocaron `pathFor`/`dynamicPathFor` en sí — se usan también para extraer parámetros en
`generateStaticParams`, donde un `/` final de más rompe el matching por substring.

## Cómo lo prevengo

- Con `trailingSlash: true` + un hosting que redirige la versión sin barra — **toda URL
  absoluta armada fuera de `<Link>` necesita el `/` final a mano.** No asumir que la
  config del framework se propaga sola ni que una plataforma concreta sea la excepción:
  verificar la respuesta HTTP real después de cada migración o cambio de hosting.
- Nuevo ítem en [[SEO Launch]]: verificar con `curl -sI` que una muestra de URLs del
  sitemap responda 200, no 3xx. `sitemap.ts` y `llms.ts` son los puntos ciegos reales
  porque nunca pasan por `<Link>`.
- Antes de dar por cerrada una migración de hosting que cambia el manejo de trailing
  slash: grep de `<a href=` planas contra rutas internas del proyecto — son las que
  ningún test de navegación manual atrapa.

## Enlaces

[[SEO Launch]] · [[NextJS]]
