---
tags: [patterns, seo, contenido, escala]
updated: 2026-08-01
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

La puerta se evalúa **por entidad, no por categoría ni por lote**. Si de una categoría
solo califican 60 de 400, entran esos 60 y el resto espera — nunca se baja el criterio
para completar una cuota por categoría. Publicar para rellenar es el mismo error que
publicar sin dato real, solo que a nivel de grupo en vez de a nivel de campo.

### El test que resume la puerta

> **¿Valdría la pena publicar esta página si ninguna de sus hermanas existiera?**

Si la respuesta es no, la página es un subproducto de la plantilla, no contenido.

### Umbrales concretos

Los números importan porque "poco contenido único" no es operable y un porcentaje sí:

| Métrica | Umbral | Acción |
|---|---|---|
| Contenido único por página | **< 40%** | Marcar como flaca, riesgo real de penalización |
| Contenido único por página | **< 30%** | Freno duro: no se publica |
| Páginas generadas sin revisión humana | 100+ | Auditar una muestra antes de publicar |
| Páginas sin justificación explícita | 500+ | Freno duro |
| Palabras por página | < 300 | Revisar: probablemente no se sostiene |

**Cómo se mide el porcentaje**: palabras exclusivas de esta página sobre el total de la
página, comparando contra el resto del conjunto generado. Cabecera, pie y navegación
se excluyen del cálculo; **el texto de la plantilla se incluye**. Ese detalle es el que
hace que el número sea honesto: si el 70% de la página es boilerplate compartido,
ninguna variación de redacción lo salva.

**Muestra humana**: revisar a mano entre el 5% y el 10% de las páginas generadas antes
de publicar. No hay verificación automática de "esto se lee como plantilla".

### Publicar en tandas, no de golpe

Lotes de 50-100 páginas, y **esperar 2-4 semanas** mirando indexación antes de ampliar.
Nunca publicar 500+ páginas generadas de una vez.

Dos razones: si la calidad está mal, el daño queda acotado a la primera tanda en vez de
al dominio entero; y un dominio nuevo tiene presupuesto de rastreo chico, así que las
páginas de más no se rastrean antes, solo diluyen.

Consecuencia sobre el orden: el enlazado interno y las prioridades del sitemap tienen
que **empujar primero lo que más vale**, no repartir parejo entre categorías.

---

## Qué escala bien y qué no

La forma de la página decide el riesgo, antes de cualquier detalle de redacción:

| Escala bien ✅ | Por qué |
|---|---|
| Ficha de producto | Specs propios, datos verificables por entidad |
| Página de integración / compatibilidad | Documentación real y distinta en cada caso |
| Glosario | Definición propia con ejemplos, 200+ palabras |
| Herramienta / plantilla descargable | La utilidad es el contenido |
| Página derivada de datos | Estadística o análisis propio por registro |

| Riesgo de penalización ❌ | Por qué |
|---|---|
| Páginas de ubicación con solo la ciudad cambiada | Doorway pages, es un algoritmo dedicado |
| "Mejor [X] para [Y]" sin valor específico de Y | Flaca por construcción |
| "**Alternativa a [competidor]**" sin comparación real | Tiene que haber datos comparados, no una tabla generada |
| Cualquier página donde >60% del texto es plantilla compartida | El umbral de unicidad no se alcanza |

La tercera fila del segundo cuadro es la que más me toca: **una página de comparación
"A vs B" armada desde dos fichas de specs es exactamente el patrón riesgoso.** Una
comparación merece URL propia cuando hay un veredicto — cuál conviene a quién y por
qué — no cuando hay dos columnas enfrentadas.

Mismo criterio para el cruce entidad × dimensión: si la respuesta es "sí, funciona" y
nada más, va como fila en la ficha, no como página.

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
- Lo que no pasa la puerta de calidad no entra, aunque exista y responda 200.
- **Límite del protocolo: 50.000 URLs o 50 MB sin comprimir por archivo**, lo que
  llegue primero. Pasado eso, índice de sitemaps.

---

## La restricción que bloquea

Cuando hay varias cosas para arreglar, **primero va el defecto que bloquea a los demás**,
aunque sea el menos interesante. Casi siempre es uno técnico: no indexable, canonical
ausente, duplicados por locale, robots bloqueando assets.

En este modelo eso es literal: sin indexación no hay ranking, sin ranking no hay cita
de IA, y ninguna mejora de contenido se mide. El plan de acción es un grafo de
dependencias, no una lista de deseos ordenada por entusiasmo.

Y al revés: **una recomendación que contradice lo que Google ya muestra para esa
intención pierde.** La SERP es la preferencia revelada de Google para esa consulta; si
mi plan discrepa, o tengo un motivo explícito por el que este sitio es la excepción, o
el plan está mal.

---

## Antipatrones que ya pagué

| Antipatrón | Qué produce |
|---|---|
| Plantilla fija con el nombre cambiado | Thin content, rechazo de AdSense |
| Poda de contenido por antigüedad | 404 sobre URLs indexadas |
| `lastmod` con la fecha del build | Google ignora la señal |
| Ruta traducida que hereda el idioma | Duplicados por producto cartesiano |
| Valor por defecto cuando falta el dato | Datos falsos a escala |
| Tratar el selector de idioma en la raíz como error de indexación | No es antipatrón de Google — `x-default` está pensado para eso. Ver [[2026-08-14 - Selector de idioma en la raiz no es antipatron de Google]] |
| Datos de ejemplo del scaffold publicados | Productos inventados en producción |
| Traducir el catálogo y contarlo como contenido nuevo | Cae en scaled content abuse |
| Publicar cientos de páginas de una sola vez | Si la calidad está mal, se lleva el dominio |
| Dato "vendible" (precio, specs) como eje textual sin anclar el nicho | Atrae intención genérica ajena al nicho, diluye posicionamiento — [[2026-08-13 - Precio destacado sin filtro de nicho diluye el posicionamiento]] |

---

## Enlaces

[[Content Pipelines]] · [[Internationalization]] · [[SEO Launch]] · [[NextJS]] ·
[[AI Crawler Discoverability]] · [[Generative Engine Optimization]] · [[E-E-A-T]]
