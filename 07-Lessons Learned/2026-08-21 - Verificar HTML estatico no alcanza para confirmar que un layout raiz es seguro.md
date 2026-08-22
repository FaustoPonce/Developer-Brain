---
tags: [lesson, nextjs, react, hydration, checklist]
updated: 2026-08-21
severity: alto
---

# Verificar el HTML estático no alcanza para confirmar que un layout raíz es seguro

## Síntoma

Ninguno al momento de deployar — el chequeo previo había dado bien. El usuario
reportó el sitio roto navegando en vivo: la home cargaba visualmente (el HTML
inicial se ve, porque el server-render sí es correcto) pero **ningún botón ni
link respondía**. Confirmado con Playwright contra producción: 0 botones, 0
links, body vacío después de la hidratación — React tira el error #418 y
desmonta el árbol entero.

## Impacto

La home (y cualquier página real del sitio) quedó inutilizable para el 100% de
los visitantes reales durante el tiempo que el deploy estuvo en vivo. Mucho más
grave que el problema original que se estaba arreglando (una 404 genérica en vez
de una propia) — cambiar algo del layout raíz para arreglar un problema chico
rompió algo mucho más caro.

## Causa raíz

Se agregó un `app/layout.tsx` (raíz) nuevo, arriba de `[lang]/layout.tsx` (que ya
renderizaba su propio `<html lang={locale}>`), para poder servir un
`app/not-found.tsx` real. La verificación fue: build real + `grep` sobre el HTML
exportado, confirmando **un solo `<html>` por página** y el `lang` correcto en
cada una. Pasó limpio.

Pero el HTML estático es lo que el servidor manda en el primer request — no es lo
que React arma en el cliente al hidratar. React necesita que el árbol de
componentes que reconstruye en el cliente (a partir del JS bundle) coincida
estructuralmente con lo que el servidor efectivamente renderizó para ese layout.
Con dos layouts declarando `<html>`/`<body>` en la misma rama (uno raíz, uno
anidado), el HTML final que ve un `grep` puede salir "aplanado" a una sola
declaración sin que el árbol de componentes en memoria coincida con eso — el
cliente arranca esperando una estructura que el server, en la práctica, no le dio
de la forma que React espera reconstruir. El resultado es el error de hidratación
#418 (mismatch de árbol), y React reacciona desmontando la página entera en vez de
dejar algo roto a medias.

## Por qué no lo vi antes

`grep` sobre el HTML exportado y una captura de Playwright del HTML ya renderizado
(sin revisar la consola del browser) dan **falsos positivos perfectos** acá: los
dos verifican el resultado final visible, no el proceso de hidratación en sí. Ni
`typecheck`, ni `lint`, ni `build` detectan un mismatch de hidratación — compila
limpio, el HTML sale bien formado. Un screenshot de Playwright tomado
inmediatamente después de `goto()` también puede verse "bien" si se saca antes de
que el error tire y desmonte — hay que esperar a que la hidratación termine y
**leer la consola/errores de página**, no solo mirar el DOM.

## Fix

Revertir el layout raíz nuevo (volver al estado sin él) fue la respuesta correcta
e inmediata — un sitio roto en producción no espera a un diagnóstico prolijo.
Diagnóstico completo después, no antes, de restaurar el servicio.

## Cómo lo prevengo

- **Cualquier cambio a un layout raíz (`app/layout.tsx`) en un proyecto con más de
  un layout declarando `<html>`/`<body>` en la misma rama del árbol** se verifica
  con un browser real, no solo con el HTML exportado:
  1. Levantar el build (local o el deploy real).
  2. Playwright: `goto()`, esperar a que la red se calme (`networkidle` o un
     timeout corto después de la carga), **y recién ahí** contar elementos
     interactivos reales (`button`, `a`) — no alcanza con que el texto se vea.
  3. Capturar `page.on('console')` y `page.on('pageerror')` **antes** de navegar,
     y revisar que no haya errores — un error de hidratación real aparece ahí
     aunque el DOM final se vea correcto.
- Esto es un caso particular de un patrón más general: **"el HTML se ve bien" y
  "la página funciona" son verificaciones distintas.** La primera prueba el
  render del servidor; la segunda prueba todo el ciclo cliente. Para cualquier
  cambio de layout, root, o algo que toque cómo se hidrata la app — probar la
  segunda, no asumirla a partir de la primera.
- Nuevo ítem en [[SEO Launch]] / checklist de deploy: cambios de layout raíz se
  prueban con Playwright real (consola + interactividad post-hidratación) antes
  de deployar, no solo con `curl`/`grep` sobre el HTML.

## Enlaces

[[NextJS]] · [[2026-08-18 - not-found.tsx fuera de la raiz nunca se sirve en export estatico]] · [[SEO Launch]]
