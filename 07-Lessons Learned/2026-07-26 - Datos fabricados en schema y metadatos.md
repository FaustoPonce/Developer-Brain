---
tags: [lesson, seo, schema, datos]
updated: 2026-07-26
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

Dos formas del mismo error, ambas nacidas de querer que el campo *no quede vacío*:

1. **Valor constante inventado.** Declarar en el schema un precio fijo y disponibilidad
   "en stock" para cientos de entidades, sin que ese dato venga de ningún lado.
2. **Fallback silencioso.** Cuando falta el dato real, poner uno plausible por defecto.
   Un valor de rendimiento "60" cuando no hay medición, una fecha de hoy cuando el
   parseo falla. La página afirma con seguridad algo que nadie verificó.

El fallback es peor que el vacío: un campo ausente es honesto, un campo con un número
inventado es una afirmación falsa, y encima indistinguible de una verdadera.

## Por qué no lo vi antes

Un fallback se siente defensivo — "que no rompa". Pero en un sitio cuyo producto **es
la información**, un dato inventado no es robustez, es el bug.

El síntoma también está diseñado para no verse: el fallback existe precisamente para
que nada falle.

## Fix

- **Sin dato real, el campo no se emite.** Nada de constantes de relleno en datos
  estructurados.
- **Los fallbacks no inventan contenido.** Si el parseo falla, se registra y se omite;
  no se sustituye por un valor plausible.
- Cuando la fuente es ambigua, el texto **degrada a lo que sí es demostrable** en vez
  de precisar de más. Preferible "admite juego cruzado entre plataformas compatibles"
  que enumerar plataformas que la fuente no confirma.

## Cómo lo prevengo

- Buscar en el código los operadores de valor por defecto (`||`, `??`) dentro de
  metadatos, datos estructurados y texto visible. Cada uno se justifica o se saca.
- **Principio general:** en un sitio de datos, prefiero un hueco a una mentira.
  El hueco se nota y se arregla; la mentira escala.
- Vale también para el contenido generado por IA: ver [[Content Pipelines]].

## Enlaces

[[Programmatic SEO]] · [[Content Pipelines]] · [[Validation]] · [[Coding Principles]]
