---
tags: [prompt, contenido, ia, seo]
updated: 2026-07-26
model: gemini flash (funciona igual con sonnet)
status: active
---

# Artículo original a partir de una fuente

Convierte un titular y el texto de una fuente en un artículo propio, publicable,
sin inventar datos. **Corriendo en producción a diario**, no es teoría.

---

## Cuándo lo uso

Pipelines de contenido: tengo un titular y un enlace, y necesito un cuerpo propio.
Copiar la fuente es contenido duplicado; escribir sin la fuente es inventar.

---

## Prompt

```
Sos editor de {{descripción del sitio y su audiencia}}.
Escribí un artículo ORIGINAL y genuinamente útil en {{idioma}}.

Reglas:
- Basalo ÚNICAMENTE en el texto de la fuente oficial que está más abajo.
- Reformulá todo con tus palabras; no copies frases de la fuente.
- NO inventes datos concretos (fechas exactas, cifras, nombres, precios) que no
  estén en la fuente. Ante la duda, quedate en lo general.
- Audiencia: {{audiencia}}. Tono: claro, útil, levemente entusiasta.
  Extensión: 250-400 palabras.
- Agregá valor práctico: qué significa para el lector y cómo aprovecharlo,
  sin fabricar detalles.
- No agregues llamadas a la acción ni enlaces externos; el sitio pone los suyos.

TÍTULO: {{título}}
TEXTO DE LA FUENTE:
"""
{{texto de la fuente, recortado a ~3500 caracteres}}
"""

Devolvé SOLO JSON válido (sin bloques de código) con EXACTAMENTE esta forma:
{"excerpt":"resumen de una oración, menos de 160 caracteres, en {{idioma}}",
 "intro":"un párrafo corto de apertura",
 "sections":[{"heading":"título corto","body":"uno o dos párrafos"}]}
Usá 2 o 3 secciones.
```

---

## Las cuatro cosas que lo hacen funcionar

1. **Grounding.** Se le pasa el texto real de la fuente. Sin eso, el modelo rellena
   con plausibilidades y el sitio publica datos falsos.
2. **Prohibición explícita de inventar**, con la lista de qué cuenta como dato concreto.
   "No inventes" solo no alcanza; hay que enumerar.
3. **Forma de salida exacta**, y **validarla** al recibirla. La respuesta de un modelo
   es input externo: se parsea, se valida la forma, y si no cumple se descarta.
   Ver [[Validation]].
4. **La regla de reformular es parte del contrato**, no un detalle de estilo: copiar
   la fuente es contenido duplicado y arruina el propósito.

---

## Variante sin fuente

Cuando no se pudo obtener el texto (la fuente cambió el HTML, dio timeout), se cambia
la primera regla por:

```
- Basalo en conocimiento general y perenne sobre este tipo de {{tema}}.
  No hay texto de fuente disponible: escribí un panorama general y prudente.
```

Y el resultado se **marca como no fundamentado**, para poder distinguir después qué
artículos tienen respaldo y cuáles no. Esa marca es la que permite decidir si entra
al sitemap. Ver [[Programmatic SEO]].

---

## Limitaciones

- **Hay que leerlo antes de confiar.** El modelo respeta la prohibición de inventar
  la mayoría de las veces, no todas.
- Recortar la fuente a ~3500 caracteres controla el costo; con fuentes largas se pierde
  el final.
- Con cuota gratuita conviene una cadena de modelos y un tope por corrida:
  se degrada solo ante 429, y cualquier otro error aborta.
  Ver [[Content Pipelines]].

---

## Enlaces

[[Content Pipelines]] · [[Programmatic SEO]] · [[Validation]]
