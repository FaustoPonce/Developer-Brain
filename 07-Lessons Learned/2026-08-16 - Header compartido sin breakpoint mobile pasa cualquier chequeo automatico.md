---
tags: [lesson, ui, responsive, nextjs, tailwind]
updated: 2026-08-16
severity: alto
---

# Header compartido sin breakpoint mobile pasa cualquier chequeo automático

## Síntoma

No vino de un reporte ni de una métrica caída — surgió al preguntarme "¿esto es
responsive de verdad?" sobre un sitio que llevaba semanas en producción. Auditando
con Playwright a 6 anchos estándar (320/360/375/390/412/430px), el header generaba
**scroll horizontal en el 100% de los anchos probados**, en los dos idiomas. Hasta
en el ancho más grande probado (430px) sobraban ~15px.

## Impacto

El header vive en el layout raíz: no era el bug de una página, era el de **todas**
(2500+ páginas generadas comparten el mismo componente). En los anchos más chicos el
botón de cambiar tema quedaba parcial o totalmente fuera de la pantalla — no era solo
feo, era inutilizable para una porción real del tráfico mobile.

## Causa raíz

El resto del sitio sí seguía un patrón mobile-first consistente y deliberado
(`grid-cols-2` por defecto con `sm:grid-cols-4`, `flex-wrap` en breadcrumbs y chips,
inputs `w-full`) — ese patrón nunca llegó a la fila del header porque es la única
pieza de layout que se escribe una vez, al principio del proyecto, y después casi no
se vuelve a tocar visualmente: cada sesión posterior edita contenido o datos, no
chrome. Dos strings largos (`"Browse the full directory"` en vez de una palabra corta,
`"English"/"Español"` completos en vez de abreviados) fueron el gatillo concreto, pero
el problema de fondo es que la fila nunca tuvo `flex-wrap` ni un breakpoint — cualquier
string un poco más largo la iba a romper tarde o temprano.

## Por qué no lo vi antes

Ningún chequeo automático del proyecto (typecheck + lint + build + verificación de
SEO) puede detectar overflow horizontal: es un problema de layout renderizado, no de
tipos ni de rutas. Tampoco lo iba a atrapar la forma habitual de "probar en el
browser": angostar la ventana de DevTools en un monitor de escritorio rara vez llega
hasta los 320-360px reales, y el ojo tiende a parar en cuanto "se ve mal" en vez de
tocar los anchos mínimos reales de un dispositivo.

## Fix

Playwright headless contra el build real, en los 6 anchos estándar, midiendo
`document.documentElement.scrollWidth - clientWidth` en el DOM — no una estimación
visual ni un cálculo de anchos a mano. El fix en sí fue puntual: colapsar el link de
navegación a ícono-solo por debajo de `sm`, y las etiquetas de idioma a su código de
2 letras por debajo de `sm`. Nunca se ocultó la función, solo la verbosidad.

## Cómo lo prevengo

- Cualquier componente de **chrome persistente** (header, footer, nav) se prueba a
  los anchos mínimos reales (320px en adelante), no reduciendo la ventana del
  navegador a ojo — un bug ahí no es de una página, es de todas las que compartan el
  layout.
- Playwright headless (`npx playwright install chromium` si no está, corre contra
  `next dev` local) da una medición objetiva en segundos — más rápido que pedir
  capturas de un celular real y no depende de tener uno a mano.
- Nuevo ítem en [[SEO Launch]]: medir overflow horizontal real en anchos estándar
  antes de lanzar, específico para lo que vive en el layout raíz.

## Enlaces

[[Components]] · [[SEO Launch]] · [[NextJS]]
