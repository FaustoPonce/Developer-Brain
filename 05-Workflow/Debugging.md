---
tags: [workflow, debugging, process]
updated: 2026-07-26
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

`console.log` está bien. El debugger es mejor cuando hay que inspeccionar scope.
Loguear objetos completos, no campos sueltos: `console.log({ user, input, result })`.

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

- [[Error Handling]] · [[Git]] · [[Code Review]]
