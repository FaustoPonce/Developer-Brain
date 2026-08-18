---
tags: [lesson, seo, canonical, checklist]
updated: 2026-08-15
severity: medio
---

# Regla de canonical cumplida solo en las páginas "vistosas"

## Síntoma

Search Console: "Duplicada: el usuario no ha indicado ninguna versión canónica" sobre
la home y las páginas legales/de trámite (privacy, terms, cookies, contacto).

## Impacto

Esas páginas competían entre sí por indexación sin que hubiera forma de que Google
supiera cuál era la versión válida — mismo `<title>` heredado del layout, cero
`<link rel="canonical">` en ninguna.

## Causa raíz

La regla "toda página necesita canonical" **ya estaba** en el checklist desde julio.
Se cumplió en las páginas de juegos, el directorio, los hubs de categoría — todas
páginas generadas o con lógica propia, escritas prestando atención al SEO desde el
día uno. Las páginas de trámite (privacy, terms, cookies, contacto) se escribieron
copiando la estructura más simple de la que había alrededor, sin `generateMetadata`
propio, y nadie volvió a auditarlas contra la regla porque no eran el foco de
ninguna sesión de trabajo.

## Por qué no lo vi antes

El checklist se aplicaba mentalmente sobre "las páginas importantes" —las que
generan tráfico, las que se tocan seguido— y las páginas legales quedan fuera de esa
categoría por diseño: nadie las mira, nadie mide su tráfico, se las trata como
código muerto administrativo. Pero para Google no son invisibles: son URLs con
`<title>` y sin canonical como cualquier otra, y el motor las evalúa igual.

Una regla de checklist que se verifica "a ojo, mirando la página que estoy tocando
ahora" no cubre las páginas que nunca se tocan.

## Fix

Grep de una sola línea que confirma el estado real, no la memoria de qué se hizo:

```
grep -L "canonical" src/app/**/page.tsx
```

Cualquier archivo que aparezca sin ese grep matcheando es una página sin canonical,
sin importar si es "importante" o no.

## Cómo lo prevengo

- El ítem de canonical del checklist se verifica con un grep sobre **todas** las
  rutas del proyecto antes de cerrar cualquier auditoría de SEO, no solo sobre las
  páginas que motivaron la sesión. Ver [[SEO Launch]].
- Página de trámite nueva (legal, contacto, 404, etc.) entra al mismo
  `generateMetadata` con canonical que cualquier otra ruta — no hay categoría de
  página exenta de la regla.

## Enlaces

[[SEO Launch]] · [[Programmatic SEO]] · [[NextJS]]
