---
tags: [lesson, ai-research, sourcing, datos, content-pipelines]
updated: 2026-08-10
severity: medio
---

# Cita real pero del producto equivocado en research de IA

## Síntoma

Un informe de Deep Search (Gemini) entrega, para un producto X, una URL de comunidad
(Reddit, foro, review) que es 100% real, carga, y trata sobre un tema genuino — pero el
producto que describe esa URL **no es X**, es otro modelo, a veces de otra marca. La URL
no está rota ni es inventada, así que pasa cualquier chequeo automático de "¿existe este
link?". Solo se detecta leyendo el título/slug con atención y comparándolo contra el
nombre exacto del producto.

Ejemplos reales de una sola tanda de auditoría (proyecto Hardware Compat, 2026-08-05):
Roland Bridge Cast X citaba un hilo de r/elgato sobre un producto de Elgato; Magewell
"USB Capture HDMI Gen 2" citaba una review de "Magewell Pro Capture" (línea PCIe
completamente distinta a la línea "USB Capture" que se estaba describiendo); un monitor
Lenovo citaba un post de Reddit que era, literalmente, un aviso de venta de notebooks
usados sin ninguna mención al producto.

## Sub-patrón: la línea gaming real de una marca contamina a sus SKUs hermanos

Variante más específica y más peligrosa que la mezcla genérica de arriba, encontrada en
una auditoría posterior (Hardware Compat, 2026-08-10) sobre ~190 citas de 10 informes:
**~70% resultaron fabricadas o mal atribuidas**, muy por encima de la tasa histórica
(15-45%). El mecanismo: cuando una marca tiene una línea **real y genuinamente diseñada**
para handhelds/gaming (iVanky **VCA23** "8-in-1 Gaming Dock", UGREEN **CM800** "Steam Deck
Docking Station", Baseus **GamerX** / "6-in-1 Steam Deck Dock"), el texto de marketing
real de ESA línea aparece pegado, por resellers o por el propio research, en las citas de
SKUs completamente distintos y no-gaming de la **misma marca** (4 productos de marcas
distintas — Plugable, Sabrent, WAVLINK, Kensington — con la misma frase textual de B&H que
en realidad es de iVanky VCA23; media docena de variantes UGREEN Revodok/Pro con texto que
es en realidad de la línea "Steam Deck Docking Station").

A diferencia del caso genérico de arriba (similitud semántica azarosa), acá hay una razón
estructural: la marca sí tiene un producto real para ese caso de uso, así que la cita
"suena" perfectamente plausible y specíficamente coherente con el tema — no es un
error obviamente ajeno como el aviso de venta de notebooks usados, es *casi* correcto, lo
que baja la guardia del auditor.

## Impacto

Si no se audita, el sitio publica una "confirmación de compatibilidad de la comunidad"
con una fuente que parece sólida (dominio real, hilo real) pero que en realidad no dice
nada sobre el producto en cuestión. Es más peligroso que una URL inventada porque
sobrevive un chequeo superficial de "¿el link funciona?".

## Causa raíz

El modelo de research (Gemini Deep Search) arma el informe recuperando resultados de
búsqueda por similitud semántica/textual (mismo fabricante, línea de producto con nombre
parecido, mismo dominio Reddit temático) y los adjunta como si confirmaran el producto
exacto que está describiendo en ese párrafo, sin verificar que el título del hilo
mencione el modelo específico.

## Por qué no lo vi antes

La URL en sí es indistinguible de una cita válida: dominio conocido (reddit.com,
youtube.com), formato de hilo real, tema temáticamente relacionado (mismo fabricante,
categoría de producto similar). El error no está en el link, está en el emparejamiento
entre el link y el párrafo al que fue pegado. Automatizar la detección con un chequeo de
"URL responde 200" no lo agarra nunca.

## Fix

- **Nunca cargar una cita de comunidad sin leer el título/slug de la URL y confirmar que
  menciona el modelo exacto** (no la marca sola, no una línea de producto parecida, no
  "genérico + tema"). Alcanza con el slug de la URL casi siempre — no hace falta abrir el
  link completo.
- Si el título no menciona el modelo exacto, la cita se descarta. El producto se carga
  igual (specs de fabricante), simplemente sin esa salvedad de compatibilidad de
  comunidad — un producto sin caveat es preferible a uno con un caveat mal atribuido.
- Instrucción reforzada en el prompt de research (probada, redujo la tasa de error en la
  tanda siguiente): pedirle explícitamente al modelo que abra el hilo, confirme que
  menciona el producto exacto, y omita la cita si tiene cualquier duda — "mejor sin
  caveat que con uno fabricado".
- Para el sub-patrón de línea gaming real: si la marca tiene un producto "para Steam
  Deck/ROG Ally/gaming" explícito en su catálogo, sospechar de inmediato de cualquier cita
  de compatibilidad handheld en OTRO SKU de la misma marca — verificar la URL exacta antes
  de cargar, no asumir que la cita es del SKU correcto solo porque el nombre coincide en el
  título del bloque. Nombrar la línea gaming conocida de la marca explícitamente en el
  prompt de research, si se conoce de antemano, redujo la tasa de error en la tanda
  siguiente (varios informes se auto-corrigieron y rechazaron por su cuenta exactamente
  este patrón).

## Cómo lo prevengo

- Regla de auditoría fija para cualquier research externo con citas de comunidad: leer
  el slug de cada URL antes de cargarla, no solo verificar que resuelve.
- Al escribir prompts de Deep Search que van a alimentar una base de datos pública,
  incluir siempre la instrucción explícita de verificar coincidencia de producto en el
  título del hilo, no asumirla por tema/marca.
- Aplica a cualquier pipeline que use research de IA como fuente de "evidencia social" o
  citas — no es específico de Hardware Compat.

## Enlaces

[[2026-07-26 - Datos fabricados en schema y metadatos]] · [[Content Pipelines]] ·
[[2026-08-11 - Auto-cita circular y cita inventada de cero en research de IA]]
