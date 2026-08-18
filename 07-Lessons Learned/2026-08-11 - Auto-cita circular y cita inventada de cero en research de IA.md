---
tags: [lesson, ai-research, sourcing, datos, content-pipelines]
updated: 2026-08-11
severity: alto
---

# Auto-cita circular y cita inventada de cero en research de IA

## Síntoma

Dos fallas nuevas encontradas en la misma tanda de auditoría (Hardware Compat, Fase 5,
2026-08-11), distintas del ya documentado [[2026-08-05 - Cita real pero del producto
equivocado en research de IA]] (que es sobre URL real + producto equivocado):

1. **Auto-cita circular.** El research trae como "evidencia externa" de que un producto
   puntual funciona con X, la propia página agregada del sitio que se está construyendo
   (`https://compatcheck.app/en/usb-hub/`, mostrando el conteo total "91 confirmed: 27
   for Steam Deck..."). El modelo de Deep Search indexó la página ya publicada del propio
   proyecto y la devolvió como si fuera una fuente independiente. 3 casos en una sola
   tanda (Belkin AVC002, Plugable USBC-7IN1, Satechi Pro Hub Slim).
2. **Cita fabricada de cero, no solo mal atribuida.** Un informe citó un GitHub issue
   real (`LoveRetro/NextUI#485`) con una cita textual completa atribuida a él sobre un
   DAC USB-C (FiiO KA11) y Steam Deck. Al verificar la página real con WebFetch, ese
   contenido **no existe en la fuente** — el issue trata de un DAC genérico en un
   dispositivo distinto. A diferencia del caso "producto equivocado" (donde la URL real
   sí dice algo, solo que sobre otro SKU), acá la URL es real pero el texto citado es
   pura invención — el modelo generó una cita plausible y le puso un link real al lado.

## Impacto

Ambos son más peligrosos que una URL rota o un dato vacío: pasan cualquier chequeo
automático de "¿el link existe/responde 200?", y el caso 1 además tiene una trampa
específica — como el sitio propio ya está indexado y creciendo, cuantas más páginas
agregadas publique, más fácil es que el research las recupere y las cite como si fueran
prueba externa de sí mismas (loop de auto-confirmación, cada vez más difícil de detectar
a simple vista a medida que el sitio crece).

## Causa raíz

1. Auto-cita: el corpus de búsqueda del modelo no distingue "página que yo mismo indexé
   de este proyecto" de "fuente externa independiente" — cualquier página pública con el
   texto correcto (nombre de marca + "Steam Deck confirmed") es candidata a citarse, sin
   verificar que el dominio no sea el del propio sitio que se está alimentando.
2. Cita inventada: mismo mecanismo de fondo que el caso de producto equivocado (armar
   una cita "plausible" sin abrir la fuente completa), llevado un paso más allá — en vez
   de pegar una cita real de otro producto, el modelo generó texto nuevo con la forma de
   una cita real y lo ató a una URL que sí existe pero no la contiene.

## Fix

- **Antes de cargar cualquier cita, chequear que el dominio de la URL no sea el propio
  sitio del proyecto.** Regla mecánica, cero ambigüedad — si aparece `compatcheck.app`
  (o el dominio del proyecto que sea) como "fuente externa", se descarta completo sin
  excepción.
- **Verificar con WebFetch el contenido real de cualquier cita textual "completa" antes
  de cargarla**, no solo confirmar que la URL resuelve — sobre todo si la cita viene con
  comillas extensas y coincide sospechosamente bien con el caso de uso exacto que se
  está buscando (señal de que puede estar armada para encajar, no encontrada).
- Instrucción a agregar en prompts de research futuros: excluir explícitamente el
  dominio propio del sitio como fuente válida, y pedir que toda cita textual venga
  acompañada de suficiente contexto (no solo la frase aislada) para poder verificarla
  contra la fuente real.

## Cómo lo prevengo

- Regla de auditoría fija, sumada a la ya existente de leer el slug de cada URL: **chequear
  el dominio contra la lista de dominios propios del usuario antes de aceptar cualquier
  cita como "evidencia externa".**
- Para citas textuales largas o inusualmente perfectas, abrir la fuente completa
  (WebFetch) en vez de confiar en que el modelo la transcribió bien — el costo de
  verificar es bajo comparado con publicar una cita inventada de cero.
- Aplica a cualquier pipeline que use research de IA para poblar contenido de un sitio
  que ya está en producción y siendo indexado — el riesgo de auto-cita crece con el
  tiempo, no es un caso aislado de una sola tanda.

## Enlaces

[[2026-08-05 - Cita real pero del producto equivocado en research de IA]] ·
[[2026-07-26 - Datos fabricados en schema y metadatos]] · [[Content Pipelines]]
