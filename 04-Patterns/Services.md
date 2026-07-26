---
tags: [patterns, backend, business-logic]
updated: 2026-07-26
status: draft
---

# Services

> Donde vive la lógica de negocio. La capa que más me importa que esté limpia.

---

## Qué es un service

Una función (o módulo de funciones) que **ejecuta un caso de uso completo**:
valida reglas, orquesta repositorios y clientes externos, y devuelve un resultado.

No sabe de HTTP. No sabe de SQL. Ver [[Clean Architecture]].

---

## Forma

Funciones exportadas, no clases con estado.

Un service se lee siempre en el mismo orden, y las guardas van **antes** que los efectos:

1. **Cargar** lo que necesita para decidir.
2. **Guardas primero:** cada regla que puede rechazar la operación, con salida temprana.
   Nada se escribe ni se cobra hasta que pasaron todas.
3. **Calcular** con funciones puras.
4. **Ejecutar** los efectos (escribir, cobrar, notificar).
5. **Devolver** un resultado tipado.

El orden importa: si un efecto ocurre antes de la última guarda, un rechazo deja el
sistema a medias — cobrado sin orden, o mail mandado sin registro.

---

## Reglas

1. **Sin `req` / `res` / `Request`.** Si el service los recibe, la capa se filtró.
2. **Sin SQL.** Eso es [[Repositories]].
3. **Retorna `Result`** para errores de negocio esperados. Ver [[Error Handling]].
4. **Extraer las reglas puras a funciones aparte** (`calculateTotal`, `canCancel`). Son las que testeo.
5. **Un service es dueño de una transacción.** Si hay 2+ escrituras atómicas, la transacción abre y cierra acá.
6. **Sin dependencias circulares** entre services. Si A y B se necesitan, hay una tercera cosa oculta.
7. **Las dependencias entran como parámetro** cuando necesito testear con doble; import directo cuando no.

---

## Naming

`verbo + sustantivo`, desde el punto de vista del negocio:

- ✅ `createOrder`, `cancelSubscription`, `refundPayment`, `publishArticle`
- ❌ `handleData`, `processStuff`, `doUpdate`, `manageUser`

Ver [[Naming]].

---

## Qué testeo acá

Esta es la capa donde el testing paga:

- Cada rama de reglas de negocio (usuario bloqueado, monto inválido, pago fallido).
- Funciones puras de cálculo, exhaustivamente.
- Repositorios y clientes externos mockeados; la lógica real, no.

Ver [[Coding Principles]] → testing por riesgo.

---

## Enlaces

- [[Clean Architecture]] · [[Repositories]] · [[Error Handling]] · [[Node]] · [[Validation]]
