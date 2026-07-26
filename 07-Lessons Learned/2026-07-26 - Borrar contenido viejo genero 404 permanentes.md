---
tags: [lesson, seo, contenido, indexacion]
updated: 2026-07-26
severity: alto
---

# Podar contenido por antigüedad generó 404 recurrentes

## Síntoma

404 que volvían a aparecer en Search Console corrida tras corrida, sobre URLs que
alguna vez habían estado indexadas y rankeando.

## Impacto

Pérdida permanente de páginas ya posicionadas. Recuperar una URL indexada que devolvió
404 durante semanas es más caro que haberla mantenido: hay que re-crawlear, re-indexar
y volver a ganar la posición.

## Causa raíz

El pipeline de contenido borraba las entradas con más de N días para mantener el archivo
de datos chico. Cada artículo borrado era una URL que Google ya tenía indexada.

El error de razonamiento: tratar el contenido como **caché** cuando en realidad es un
**compromiso público**.

## Por qué no lo vi antes

Pensaba en el tamaño del archivo de datos, no en el índice de Google. Desde adentro,
podar parece higiene. Desde afuera, cada poda es una página que desaparece.

Además el daño es diferido: la poda no rompe nada hoy, aparece semanas después como
errores en Search Console sin conexión obvia con el cambio que los causó.

## Fix

**Nunca podar por antigüedad.** Para acotar el tamaño se usa un tope por cantidad
(los N más nuevos), no un corte por fecha. El tope se elige lo bastante alto como para
que en la práctica no expulse nada que esté indexado.

Si algo *tiene* que salir del sitio, la salida ordenada es redirección 301 a lo más
parecido que quede, nunca 404 silencioso.

## Cómo lo prevengo

- **Principio:** una URL publicada e indexada es un compromiso permanente. Publicar es
  fácil, despublicar cuesta.
- Antes de borrar cualquier contenido, la pregunta es "¿esto llegó a estar indexado?".
  Si la respuesta es "no sé", se asume que sí.
- Todo borrado de contenido va acompañado de su redirección.

## Enlaces

[[Content Pipelines]] · [[SEO Launch]] · [[Deployment]]
