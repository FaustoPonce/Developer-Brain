---
tags: [patterns, validation, zod, security]
updated: 2026-07-26
status: draft
---

# Validation

> Validar en el **borde**. Adentro, los datos ya son confiables y tipados.

---

## Dónde valido

Todo lo que entra desde afuera de mi proceso:

- Bodies, query params y path params de HTTP
- Formularios
- Variables de entorno (al arranque)
- Respuestas de APIs externas ← **el más olvidado**
- Payloads de webhooks
- Datos de `localStorage` / cookies
- Archivos subidos (tipo, tamaño, contenido)

**Un tipo de TypeScript no valida nada en runtime.** `as ApiResponse` es una promesa, no una garantía.

---

## Herramienta

Zod en TS. **El schema es la fuente de verdad y el tipo se deriva de él** (`z.infer`),
nunca al revés y nunca los dos escritos a mano: se desincronizan sin avisar y el
compilador no se entera.

Todo campo lleva sus límites reales (largo máximo, rango, formato), no solo su tipo.
Un `string` sin `max` es un campo por donde entra un texto de 2 MB. Ver [[TypeScript]].

---

## Reglas

1. **Parsear, no validar.** `schema.parse(input)` devuelve el dato ya tipado; no chequear y seguir con el `unknown`.
2. **El mismo schema en cliente y servidor.** El del cliente es UX; el del servidor es seguridad.
3. **Nunca confiar en la validación del cliente.** Se puede saltear con curl.
4. **Validar respuestas de APIs externas.** Cambian sin avisar y el fallo aparece tres capas más abajo.
5. **Schemas cerca de su dominio:** `features/games/schemas.ts`. Ver [[Folder Structure]].
6. **`safeParse` en los bordes** donde quiero manejar el error; `parse` donde el fallo es un bug.
7. **Mensajes de error en el idioma del usuario**, mapeados por campo. Ver [[Error Handling]].

---

## Env vars

Se validan **una vez, al arranque**, contra un schema, y el proceso **falla si falta algo**.
Mejor romper en el deploy que a las 3 AM por un `undefined`.

Se valida el formato, no solo la presencia: una URL tiene que parsear como URL, una
clave secreta tiene que tener su prefijo. Una env var cargada mal falla igual que una faltante,
pero mucho más tarde y más lejos.

---

## Sanitización ≠ validación

- **Validar:** ¿tiene la forma correcta?
- **Sanitizar:** ¿es seguro renderizarlo/guardarlo?

HTML de usuario → DOMPurify. Nunca `dangerouslySetInnerHTML` con input sin sanitizar.
SQL → parámetros, siempre. Ver [[PostgreSQL]].

---

## Enlaces

- [[TypeScript]] · [[API Design]] · [[Error Handling]] · [[Authentication]]
