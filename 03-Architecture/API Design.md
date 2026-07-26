---
tags: [architecture, api, backend]
updated: 2026-07-26
status: draft
---

# API Design

> REST por default. Consistencia sobre elegancia.

---

## Convenciones de URL

- Sustantivos en plural: `/games`, `/users/:id/orders`.
- `kebab-case` en paths, `camelCase` en el JSON body.
- Anidar máximo un nivel: `/users/:id/orders` sí, `/users/:id/orders/:oid/items/:iid` no.
- Filtros por query string: `/games?platform=pc&sort=price&page=2`.
- Versión en el path solo si es API pública: `/v1/...`.

---

## Métodos y status codes

| Método | Uso | Éxito |
|---|---|---|
| `GET` | Leer, sin efectos | 200 / 404 |
| `POST` | Crear o acción | 201 (+ `Location`) / 200 |
| `PATCH` | Actualización parcial | 200 |
| `PUT` | Reemplazo completo | 200 |
| `DELETE` | Borrar | 204 |

Errores: `400` input mal formado · `401` sin autenticar · `403` sin permiso ·
`404` no existe · `409` conflicto de estado · `422` válido pero rechazado por negocio ·
`429` rate limit · `500` mi culpa.

**`200` con `{ "error": ... }` adentro está prohibido.**

---

## Forma de las respuestas

> **Contrato.** Todas mis APIs devuelven estas tres formas. No es un ejemplo, es la regla.

```jsonc
// Recurso único
{ "id": "...", "name": "..." }

// Colección
{
  "data": [...],
  "meta": { "total": 240, "page": 1, "perPage": 20 }
}

// Error — siempre este shape
{
  "error": {
    "code": "USER_BLOCKED",         // estable, para el cliente
    "message": "El usuario no puede ordenar",  // legible
    "details": [{ "field": "email", "message": "Formato inválido" }]
  }
}
```

El `code` es contrato. El `message` puede cambiar; el `code` no.
Ver [[Error Handling]].

---

## Reglas

1. **Validar todo input en el borde** con schema. Ver [[Validation]].
2. **Nunca exponer entidades de DB directo.** Mapear a DTO — si no, un `SELECT *` filtra el hash de la password.
3. **Paginar toda colección** desde el día 1. Default 20, máximo 100.
4. **Idempotencia** en POSTs que cobran plata (header `Idempotency-Key`).
5. **Rate limit** en endpoints públicos y de auth.
6. **CORS explícito** por origen. Nada de `*` con credenciales.
7. **Timestamps en ISO 8601 UTC.** El cliente formatea.
8. **Cambios breaking = versión nueva.** Agregar campos no rompe; sacarlos sí.

---

## Webhooks (entrantes)

- Verificar la firma **antes** de parsear el body.
- Responder `200` rápido; el trabajo pesado va a cola o background.
- Asumir entrega duplicada → procesamiento idempotente por `event.id`.
- Loguear el payload crudo antes de procesar.

---

## Enlaces

- [[Error Handling]] · [[Validation]] · [[Authentication]] · [[Clean Architecture]] · [[Node]]
