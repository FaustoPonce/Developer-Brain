---
tags: [patterns, seo, contenido, escala]
updated: 2026-07-26
status: active
---

# Programmatic SEO

> Generar cientos o miles de páginas desde datos. Es mi modelo de tráfico principal,
> y también donde más plata perdí.

---

## La trampa central

**Más URLs no es más SEO.** Google no cuenta páginas, cuenta información distinta.
Mil páginas desde la misma plantilla valen lo que una, y encima arrastran la reputación
del dominio.

La pregunta que decide si el proyecto vale la pena, antes de escribir nada:
*¿qué tiene la página 500 que no tenga la 499?* Si la única respuesta es "el nombre",
no se genera.

---

## Los cuatro requisitos de una página generada

Ninguna página sale sin los cuatro:

1. **Un dato propio que solo ella tiene.** No una variante de redacción: un dato.
   Año, precio, puntuación, estado, plataformas. Es lo único que la hace única de verdad.
2. **Redacción que varía de forma determinística.** La variante se elige con un hash
   estable del identificador de la entidad. Mismo id ⇒ mismo texto siempre: estable entre
   builds, sin desajuste de hidratación, pero distinto de sus hermanas.
   **Nunca al azar** — un texto que cambia en cada build es peor que uno repetido.
3. **Una URL canónica única**, derivada de la identidad del contenido.
4. **Que valga la pena responder la búsqueda.** Si la respuesta cabe en una palabra,
   la página necesita contexto alrededor o no se sostiene sola.

---

## La puerta de calidad

El sitemap **no lista todo lo que existe, lista lo que está listo**.

Cuando el contenido se enriquece en varias pasadas (por cuota de API, por trabajo
manual pendiente), las páginas todavía flacas siguen existiendo y siendo accesibles,
pero **no entran al sitemap** hasta tener contenido propio.

Ofrecerle a Google una página que sé que es flaca es gastar crawl budget en empeorar
mi promedio.

---

## Verdad antes que completitud

Ver [[2026-07-26 - Datos fabricados en schema y metadatos]].

- Sin dato real, el campo no se emite. Nada de constantes de relleno.
- Ningún valor por defecto plausible cuando falta el dato: eso convierte un hueco
  honesto en una afirmación falsa.
- Cuando la fuente es ambigua, **el texto degrada a lo demostrable**. Si el campo de
  origen es texto libre, la prosa habla en general en vez de precisar de más.
- Ese contrato se escribe **arriba del generador de texto**, no en la cabeza de nadie.

---

## Sitemap

- `lastmod` refleja cuándo cambió **el contenido de esa URL**, nunca cuándo se hizo el
  build. Ver [[2026-07-26 - lastmod dinamico quema la confianza de Google]].
- Contar las entradas y compararlas con `entidades × idiomas`. Si sobran, hay rutas
  generándose de más. Ver [[Internationalization]].
- Prioridades relativas coherentes, no todo en 1.0.

---

## Antipatrones que ya pagué

| Antipatrón | Qué produce |
|---|---|
| Plantilla fija con el nombre cambiado | Thin content, rechazo de AdSense |
| Poda de contenido por antigüedad | 404 sobre URLs indexadas |
| `lastmod` con la fecha del build | Google ignora la señal |
| Ruta traducida que hereda el idioma | Duplicados por producto cartesiano |
| Valor por defecto cuando falta el dato | Datos falsos a escala |
| Página raíz que solo elige idioma | La URL más fuerte del dominio, sin contenido |

---

## Enlaces

[[Content Pipelines]] · [[Internationalization]] · [[SEO Launch]] · [[NextJS]] ·
[[AI Crawler Discoverability]]
