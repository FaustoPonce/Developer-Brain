---
tags: [architecture, errors, reliability]
updated: 2026-07-26
status: draft
---

# Error Handling

> Dos tipos de error: los que **espero** y los que **no**. Se tratan distinto.

---

## La distinción

| | Esperado | Inesperado |
|---|---|---|
| Ejemplo | Email duplicado, saldo insuficiente, 404 | DB caída, bug, null inesperado |
| Cómo lo manejo | Valor de retorno tipado (`Result`) | Excepción → handler global |
| El usuario ve | Mensaje específico y accionable | "Algo salió mal" + ID de error |
| Se loguea como | `info` / `warn` | `error` + alerta |

Usar excepciones para flujo de negocio esperado convierte el código en un `try/catch` gigante.

---

## Result para lo esperado

Los errores de negocio se **devuelven**, no se lanzan: el fallo forma parte del tipo de
retorno de la función. Ver el tipo `Result` en [[TypeScript]].

La ventaja no es estética. Una excepción se puede ignorar y el compilador no dice nada;
un `Result` obliga al caller a chequear el caso de fallo antes de poder tocar el dato.
El error deja de depender de que yo me acuerde de manejarlo.

Los errores posibles de cada operación se declaran como **códigos concretos**
(`EMAIL_TAKEN`, `USER_BLOCKED`), no como un `string` cualquiera. Así la firma de la función
documenta todo lo que puede salir mal, y agregar un caso nuevo rompe la compilación
en cada lugar que hay que actualizar.

---

## Reglas

1. **Nunca un `catch` vacío.** Si de verdad se ignora, comentario que lo explique.
2. **No tragar y devolver `null`** — pierde el motivo del fallo.
3. **Catchear solo lo que puedo manejar.** El resto sube al handler global.
4. **Handler global obligatorio:** middleware en el backend, Error Boundary en React, `error.tsx` en [[NextJS]].
5. **Contexto al loguear:** userId, requestId, input relevante. Un `"Error"` pelado no sirve para nada.
6. **Nunca filtrar internals al cliente:** stack traces, SQL y nombres de tabla se quedan en el log.
7. **Reintentar solo lo transitorio** (timeout, 5xx, rate limit) y con backoff exponencial. Reintentar un `400` es un loop infinito.

---

## En el frontend

- `error.tsx` por segmento de ruta → la app no se cae entera.
- Los errores de fetch se muestran, no se esconden: mensaje + botón de reintentar.
- Errores de validación al lado del campo, no en un toast genérico.
- Reportar a Sentry (o similar) con `userId` y ruta.

---

## Mensajes de error

Malo: `"Error 500"` · `"Algo falló"` · `"Invalid input"`
Bueno: `"No pudimos procesar el pago. Tu tarjeta no fue debitada. Probá de nuevo o usá otro método."`

Un buen mensaje dice: **qué pasó**, **si perdí algo**, y **qué puedo hacer**.

---

## Observabilidad mínima

- Logs estructurados con nivel y `requestId`.
- Alerta cuando la tasa de error supera el umbral, no por cada error.
- Un endpoint `/health`.
- Ver el log de producción **antes** de tocar código. Ver [[Debugging]].

---

## Enlaces

- [[API Design]] · [[TypeScript]] · [[Debugging]] · [[Coding Principles]]
