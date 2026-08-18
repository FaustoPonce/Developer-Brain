---
tags: [workflow, debugging, process]
updated: 2026-08-11
status: draft
---

# Debugging

> El bug siempre está donde estoy seguro de que no está.

---

## El método

1. **Reproducirlo.** Sin reproducción confiable no estoy debuggeando, estoy adivinando.
2. **Escribir qué esperaba y qué pasó.** En una frase. Suele revelar el error solo.
3. **Leer el error completo.** Entero. Incluido el `caused by` del fondo.
4. **Localizar por bisección.** Un log a la mitad del camino: ¿el dato llega bien acá? Repetir.
5. **Una hipótesis a la vez.** Cambiar dos cosas juntas y que funcione = no sé cuál era.
6. **Arreglar la causa, no el síntoma.** Un `?? 0` que tapa un `undefined` es un bug movido de lugar.
7. **Test de regresión** si el bug fue sutil o costoso.
8. **Anotarlo** en `07-Lessons Learned/` si me robó más de una hora.

---

## Chequeos de 30 segundos (antes de investigar en serio)

- ¿Guardé el archivo?
- ¿Está corriendo el server correcto? ¿El puerto correcto?
- ¿Es cache? Hard refresh, borrar `.next`, `node_modules/.cache`.
- ¿Estoy mirando producción o local?
- ¿La env var existe **en ese entorno**?
- ¿La branch es la que creo?
- ¿El paquete está instalado de verdad?

Un porcentaje vergonzoso de mis bugs muere acá.

---

## Preguntas que desbloquean

- **¿Cuándo funcionaba?** → `git log`, `git diff`, `git bisect`.
- **¿Qué cambió?** Código, dependencia, env var, dato, API externa, versión de Node.
- **¿Es el dato o el código?** Probar con input mínimo hardcodeado.
- **¿Pasa en producción también?** Si solo en prod: env, build o datos.
- **¿Qué asumí sin verificar?** Ahí está el bug, casi siempre.
- **¿El fallo es uniforme al 100% en todos los casos, o irregular?** Un chequeo
  automático que falla igual en absolutamente todos los casos (sin ninguna
  excepción) casi nunca es una propiedad de datos heterogéneos — es una propiedad
  del chequeo (tipo, comparación, parsing). Sospechar del chequeo antes que de los
  datos. Ver [[2026-08-16 - Fallo uniforme en un chequeo automatico es el chequeo, no el dato]].

---

## Herramientas por capa

| Capa | Cómo miro |
|---|---|
| Red | Network tab: status real, payload real, headers |
| React | React DevTools + Profiler para re-renders |
| Server | Logs estructurados con `requestId`. Ver [[Error Handling]] |
| DB | Loguear el SQL generado, `EXPLAIN ANALYZE`. Ver [[PostgreSQL]] |
| Build | Correr el build de producción en local |
| Estado | Loguear la transición, no el valor final |
| Navegador (hidratación, navegación, DOM en runtime) | Ver "Reproducir bugs de navegador" abajo — no adivinar leyendo código |

`console.log` está bien. El debugger es mejor cuando hay que inspeccionar scope.
Loguear objetos completos, no campos sueltos: `console.log({ user, input, result })`.

---

## Reproducir bugs de navegador sin herramienta dedicada

Bugs de hidratación, navegación client-side o estado del DOM en runtime (ver ejemplo
real en [[NextJS]] — `<html>` que pierde atributos al navegar) **no se resuelven leyendo
código**: hace falta un navegador real ejecutando el JS. Si no hay `chromium-cli` u
otra herramienta de browser automation disponible en el entorno:

1. **`npm install playwright-core`** (no `playwright` completo) — no descarga ningún
   navegador, solo el driver.
2. **Apuntar `executablePath` a un browser ya instalado del sistema** (Chrome/Edge en
   `Program Files`) en vez de dejar que Playwright baje su propio Chromium — mucho más
   rápido cuando solo se necesita reproducir un bug puntual, no correr una suite.
3. **Variable marcadora en `window`** puesta antes de la acción sospechosa
   (`window.__marker = 'x'`) para distinguir con certeza un hard reload (la variable
   desaparece, el `window` es uno nuevo) de una soft navigation client-side (sobrevive).
   Evita adivinar por timing o por el Network tab.
4. **Forzar condiciones que en el entorno de desarrollo no se dan por default**
   (`browser.newPage({ colorScheme: 'dark' })`, viewport, etc.) para reproducir el
   síntoma exacto que reportó el usuario, no una aproximación.
5. **Si el bug involucra una API de terceros detrás de Cloudflare (o cualquier
   protección anti-bot), pasar un `userAgent` de browser real al crear la página.**
   El Chrome headless de Playwright manda por default un User-Agent que dice
   literalmente `HeadlessChrome` — suficiente para que Cloudflare bloquee el request
   entero (`net::ERR_FAILED`, sin respuesta, indistinguible a simple vista de un bug
   real de CORS o de la API). Caso real: probar un formulario que postea a
   `api.web3forms.com` (CompatCheck, 2026-08-11) daba error 100% de las veces desde
   Playwright pero funcionaba con cualquier usuario real — confirmado inyectando
   `userAgent: '...Chrome/151.0.0.0...'` (sin "Headless") en `newPage()`, que
   resolvió el falso negativo al toque. **Antes de asumir que una integración con un
   servicio externo está rota, descartar esto primero** si el entorno de prueba es
   headless.

Con eso alcanza para confirmar la causa raíz con evidencia real antes de tocar código
— evita el ciclo de "arreglo lo que creo que es, el usuario me dice que sigue" cuando
el bug depende de mecánica de runtime del framework que no es obvia leyendo el código.

---

## Bugs solo en producción

Sospechosos en orden:

1. Env var faltante o distinta.
2. Diferencia build dev vs prod (tree-shaking, minificación, `NODE_ENV`).
3. Datos reales con casos que en local no existen (nulls, emojis, strings larguísimos).
4. Timezone del servidor.
5. Race condition que en local no se da por latencia distinta.
6. Cache o CDN sirviendo algo viejo.

---

## Cuándo parar

Si llevo **90 minutos sin avanzar**: caminata, o dejarlo para mañana.
Casi siempre lo resuelvo en los primeros 10 minutos del día siguiente.

Si sigo trabado: explicárselo a alguien (o a la IA) desde cero. La mitad de las veces
el bug aparece mientras escribo la explicación.

---

## Enlaces

- [[Error Handling]] · [[Git]] · [[Code Review]] · [[NextJS]]
