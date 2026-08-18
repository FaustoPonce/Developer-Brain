---
tags: [lesson, seo, contenido, search-console]
updated: 2026-08-13
severity: medio
---

# Precio destacado sin filtro de nicho diluye el posicionamiento

## Síntoma

En un catálogo de nicho (ej. "juegos buenos para Steam Deck"), decenas de consultas
tipo "how much is X price/cost" — sin ninguna mención del nicho — generan impresiones
en Search Console con posición pésima (30-90+) y cero clics. Mientras tanto, las
consultas que sí llevan el nicho en la frase (`[producto] + [nicho]`) rankean bien
(posición 4-8) y convierten.

## Impacto

Medio. No rompe nada, pero desperdicia presupuesto de rastreo y de contenido
compitiendo en una categoría de búsqueda saturada (price-tracking genérico) donde el
sitio no tiene ninguna ventaja, en vez de consolidar la categoría donde sí la tiene.

## Causa raíz

El precio es central al producto (comparador, budget finder, etc.), así que aparece
de forma prominente en cada ficha. Google indexa esa prominencia y empieza a mostrar
la página para consultas de intención puramente genérica de precio — la misma
categoría de búsqueda que dominan sitios de tracking de precios dedicados, con
volumen y autoridad muy superiores. El sitio termina compitiendo, sin proponérselo,
fuera de su categoría real.

## Por qué no lo vi antes

El precio se trató como un dato más de la ficha, no como una señal de intención de
búsqueda. La suposición implícita fue "mostrar el precio ayuda a rankear para
compradores" — cierto para compradores *dentro del nicho*, pero el mismo texto
también califica a la página para compradores genéricos que buscan cualquier precio
de Steam, un público que el sitio no puede servir mejor que un comparador dedicado.

## Fix

- No es un fix de código: es un chequeo de Search Console post-lanzamiento (ver
  checklist).
- Frasear el contenido explícito hacia el nicho, no solo hacia el precio: títulos,
  encabezados y primer párrafo deben anclar la intención en `[producto] + [nicho]`,
  no en `[producto] + [precio]` solo.
- El precio se sigue mostrando (es información real y útil), pero no como el eje
  textual dominante de la página.

## Cómo lo prevengo

- Chequear en Search Console, después de publicar, qué proporción de las consultas
  con impresiones **no mencionan el nicho**. Si son consultas genéricas de precio con
  posición mala (30+) y cero clics, es la señal de esta dilución — no hace falta
  esperar a que se acumulen decenas para reconocer el patrón.
- Al planear contenido para un catálogo de nicho: cada campo de datos "vendible"
  (precio, specs, disponibilidad) es también un imán de intención de búsqueda ajena
  al nicho. Preguntar explícitamente "¿esta consulta buscaría este sitio si no
  tuviera el nicho?" antes de destacar ese dato en el texto.
- Ítem agregado a [[SEO Launch]] → "Después de publicar".

## Evidencia

- **Steam Deck Gems** (catálogo de 800+ juegos "buenos para Steam Deck", con precio
  de Steam mostrado en cada ficha): en una auditoría de 7 meses de Search Console,
  decenas de variantes de "how much is beamng drive" / "rimworld price" / "bloons
  td 6 price" (juegos populares, sin relación específica con Steam Deck) generaron
  impresiones en posición 30-90 y 0 clics — compitiendo contra trackers de precios
  genéricos. En cambio, consultas como "f1 23 steam deck" (posición 7.94, CTR 6.25%)
  o "steam deck gems" (marca, posición 4, CTR 11.11%) — formato `[producto] +
  steam deck` — rindieron muy por encima del promedio del sitio (CTR global 0.58%).

## Enlaces

[[Programmatic SEO]] · [[E-E-A-T]] · [[SEO Launch]]
