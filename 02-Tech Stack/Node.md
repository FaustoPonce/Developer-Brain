---
tags: [stack, backend, node]
updated: 2026-07-26
status: draft
---

# Node.js

> Backend por default cuando no uso las API routes de [[NextJS]].

---

## Setup

- Versión LTS, fijada en `.nvmrc` y en `engines` del `package.json`.
- ESM (`"type": "module"`). Nada de CJS en proyectos nuevos.
- TypeScript siempre. Ver [[TypeScript]].
- Package manager: uno solo por repo, con lockfile commiteado.

---

## Estructura de un servicio

```
src/
├── routes/          # HTTP: parseo, status codes, nada de lógica
├── services/        # Lógica de negocio. Ver [[Services]]
├── repositories/    # Acceso a datos. Ver [[Repositories]]
├── schemas/         # Validación con Zod. Ver [[Validation]]
├── lib/             # Clientes externos, utils
├── config/          # Env vars parseadas y tipadas
└── index.ts
```

Regla: **el route handler no sabe de la base de datos**. Ver [[Clean Architecture]].

---

## Variables de entorno

Se parsean **una vez**, al arranque, en un único módulo (`config/env.ts`) que valida y
exporta un objeto tipado. Si falta o está mal una variable, el proceso no levanta.

Nunca `process.env.X` disperso en el código: pierdo el tipado, pierdo el default, y el
fallo aparece en runtime seis capas más abajo. Un solo punto de entrada.
Ver [[Validation]].

---

## Async

- `async/await` siempre. Nada de callbacks ni cadenas de `.then()`.
- `Promise.all` para trabajo paralelo independiente; `allSettled` si los fallos son tolerables.
- Todo await tiene dueño de su error. Ver [[Error Handling]].
- Timeout explícito en cada llamada a red — sin timeout, un servicio caído cuelga el mío.

---

## Producción

- Logs estructurados en JSON (pino). `console.log` solo en desarrollo.
- Endpoint `/health` en todos los servicios.
- Shutdown ordenado: cerrar servidor HTTP y pool de DB en `SIGTERM`.
- Nunca correr como root en el contenedor. Ver [[Docker]].
- Secrets por env var, jamás en el repo.

---

## Enlaces

- [[TypeScript]] · [[PostgreSQL]] · [[Docker]] · [[API Design]] · [[Services]]
