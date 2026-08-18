---
tags: [lesson, seo, schema, datos]
updated: 2026-08-14
severity: alto
---

# Datos fabricados en schema y metadatos

## Síntoma

Nada visible. Ese es justamente el problema: el sitio se ve bien, valida, y el daño
—pérdida de confianza del buscador, riesgo de penalización manual sobre datos
estructurados— no tiene alerta.

## Impacto

Potencialmente alto y difícil de revertir. Una penalización por datos estructurados
engañosos se arrastra a nivel dominio.

## Causa raíz

Tres formas del mismo error. Las dos primeras nacen de querer que el campo *no quede
vacío*; la tercera, de querer que *valide y rankee*:

1. **Valor constante inventado.** Declarar en el schema un precio fijo y disponibilidad
   "en stock" para cientos de entidades, sin que ese dato venga de ningún lado.
2. **Fallback silencioso.** Cuando falta el dato real, poner uno plausible por defecto.
   Un valor de rendimiento "60" cuando no hay medición, una fecha de hoy cuando el
   parseo falla. La página afirma con seguridad algo que nadie verificó.
3. **Métrica de tercero apropiada como propia.** El valor es *real* pero no es tuyo:
   emitir el puntaje de un agregador externo como `aggregateRating` **del sitio**,
   inflando un `ratingCount` (el clásico "1" con un comentario que lo justifica) para
   que el rich result valide. La reseña no es del sitio; el schema declara que sí. Es
   *self-serving review markup*, exactamente lo que Google castiga con acción manual.

El fallback es peor que el vacío: un campo ausente es honesto, un campo con un número
inventado es una afirmación falsa, y encima indistinguible de una verdadera. La tercera
forma es más traicionera todavía: las dos primeras **inventan** el dato, la tercera lo
**roba**. Para Google es el mismo delito —datos estructurados que no representan lo que
la página realmente ofrece— y el número siendo verdadero no lo salva, porque lo que se
evalúa es la **propiedad** de la reseña, no si la cifra es correcta.

## Por qué no lo vi antes

Un fallback se siente defensivo — "que no rompa". Pero en un sitio cuyo producto **es
la información**, un dato inventado no es robustez, es el bug.

El síntoma también está diseñado para no verse: el fallback existe precisamente para
que nada falle.

La tercera forma engaña por otro lado: como el número **es real y tiene fuente**, no
dispara el instinto de "no inventes datos" — se siente legítimo citar un puntaje que
existe. El error no está en la cifra, está en el `@type`: representarla como reseña
*propia* en vez de citarla como métrica *ajena*. El `ratingCount` que hay que justificar
con un comentario es la señal de que el dato no es tuyo.

## Fix

- **Sin dato real, el campo no se emite.** Nada de constantes de relleno en datos
  estructurados.
- **Los fallbacks no inventan contenido.** Si el parseo falla, se registra y se omite;
  no se sustituye por un valor plausible.
- **Una señal de reseña/rating solo se emite si es propia.** Un puntaje de un tercero
  se cita como tal —texto que nombra la fuente— nunca como `aggregateRating` del sitio.
  Reseña propia = reseñas reales sobre tu página; no las tenés, no va el markup.
- Cuando la fuente es ambigua, el texto **degrada a lo que sí es demostrable** en vez
  de precisar de más. Preferible "admite juego cruzado entre plataformas compatibles"
  que enumerar plataformas que la fuente no confirma.

## Cómo lo prevengo

- Buscar en el código los operadores de valor por defecto (`||`, `??`) dentro de
  metadatos, datos estructurados y texto visible. Cada uno se justifica o se saca.
- **Principio general:** en un sitio de datos, prefiero un hueco a una mentira.
  El hueco se nota y se arregla; la mentira escala.
- Regla operable para reseñas: **si hay que inflar el `ratingCount` para que valide,
  el dato no es tuyo y el markup no va.**
- Vale también para el contenido generado por IA: ver [[Content Pipelines]].
- **No todo dato fabricado pesa igual — hay que priorizar, no tratarlos como un bloque.**
  Dos ejes para decidir qué sacar primero cuando aparecen varios en la misma auditoría:
  - **Dónde vive:** en `schema.org`/JSON-LD, Google lo parsea como afirmación
    estructurada — ahí vive el riesgo de acción manual, va primero. Como texto visible
    nomás, es percepción de calidad, un riesgo más blando.
  - **Cuánto arrastra sacarlo:** un card aislado se saca sin más. Un dato fabricado que
    sostiene una *feature completa* (filtro, visualización repetida en todo el catálogo)
    es una decisión de producto, no una limpieza — ahí vale la pena parar y decidir
    explícitamente en vez de arrastrar el mismo criterio del card aislado.
  - Caso real: en el mismo sitio, un card de specs por-item fabricado (solo texto) se
    sacó de inmediato; un badge de rating por-item igual de fabricado, pero que sostenía
    un filtro entero del sitio, se dejó para una decisión aparte — mismo tipo de dato,
    prioridad distinta.

## Evidencia

- **Forma 3 en producción:** un sitio de comparación de precios emitía `aggregateRating`
  en 801 de 802 fichas, con el puntaje de un agregador externo como si fuera rating
  propio y `ratingCount: 1`. Search Console lo reportaba como "Fragmento de reseña"
  (cientos de impresiones) — o sea *funcionaba* como rich result, y esa era justo la
  trampa. Fix: sacar el bloque entero. Conecta con [[E-E-A-T]]: una reseña propia es
  una señal de Trust que no se puede pedir prestada.

- **Costo medido tras sacarlo (Steam Deck Gems, mismo tipo de fix):** en una auditoría
  de Search Console a 7 meses, el segmento "Fragmentos de productos" (rich result de
  `Product`/`SoftwareApplication`) concentraba el 32% de las impresiones totales del
  sitio pero solo el 20% de los clics — CTR de 0.36% contra un promedio de sitio de
  0.58%. Un rich result *sin* estrellas rinde peor que un link plano en la misma
  posición: es el costo esperable de sacar un rating que no era propio. **No es motivo
  para revertir el fix** — el riesgo que evita (acción manual por *self-serving review
  markup*) es mucho más caro que el CTR perdido — pero conviene anticiparlo al estimar
  el impacto de este tipo de corrección, no leerlo como que "la corrección salió mal".

## Enlaces

[[Programmatic SEO]] · [[E-E-A-T]] · [[Content Pipelines]] · [[Validation]] ·
[[Coding Principles]] ·
[[2026-08-05 - Cita real pero del producto equivocado en research de IA]]
