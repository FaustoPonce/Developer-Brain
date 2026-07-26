---
tags: [patterns, naming, readability]
updated: 2026-07-26
status: draft
---

# Naming

> El nombre es la documentación que no se desactualiza.

---

## Casing por tipo

| Cosa | Estilo | Ejemplo |
|---|---|---|
| Componente React | `PascalCase` | `GameCard` |
| Archivo de componente | `PascalCase.tsx` | `GameCard.tsx` |
| Hook | `camelCase` con `use` | `useGameFilters.ts` |
| Función / variable | `camelCase` | `formatPrice` |
| Tipo / interface | `PascalCase` | `CreateGameInput` |
| Constante global | `SCREAMING_SNAKE` | `MAX_RETRIES` |
| Archivo de util | `camelCase.ts` | `formatPrice.ts` |
| Carpeta | `kebab-case` | `game-filters/` |
| Tabla / columna SQL | `snake_case` | `created_at` |
| Ruta de URL | `kebab-case` | `/steam-deck-games` |
| Env var | `SCREAMING_SNAKE` | `DATABASE_URL` |
| Clase C# / método | `PascalCase` | `OrderService.CreateAsync` |

---

## Funciones

`verbo + sustantivo`. El verbo dice qué hace y qué devuelve:

| Prefijo | Significa |
|---|---|
| `get` | Devuelve algo, barato, sin fallar |
| `fetch` / `load` | Va a la red o a disco (async) |
| `find` | Puede devolver `null` |
| `create` / `build` | Construye algo nuevo |
| `is` / `has` / `can` | Devuelve boolean |
| `to` / `format` | Transforma para mostrar |
| `handle` / `on` | Event handler |

`getUser` no puede tirar 404. `findUser` puede devolver `null`. La diferencia importa.

---

## Booleanos

Siempre en positivo, con prefijo:

- ✅ `isLoading`, `hasAccess`, `canEdit`, `shouldRetry`
- ❌ `loading`, `notDisabled`, `flag`, `status` (si es boolean)

Nunca negativos: `isNotReady` obliga a leer `!isNotReady` y romperse la cabeza.

---

## Reglas

1. **Sin abreviaturas** salvo las universales (`id`, `url`, `api`, `db`).
   `usr`, `btn`, `cfg`, `tmp` → no.
2. **El largo del nombre escala con el scope.** `i` en un loop de 3 líneas está bien;
   una variable de módulo necesita nombre completo.
3. **Sin tipo en el nombre:** `userArray` → `users`. `strName` → `name`.
4. **Consistencia por sobre preferencia.** Si el proyecto ya dice `fetch`, no mezclo `load`.
5. **Un concepto, un nombre.** Si es `user` en el front, no es `account` en el back.
6. **Nombres del dominio, no técnicos:** `subscription`, no `paymentRecordEntity`.
7. **Sin `data`, `info`, `item`, `manager`, `helper`, `utils`** como nombre principal.
   Si no sé cómo llamarlo, no entendí qué hace.

---

## Nombres de archivo

- Un export principal por archivo, y el archivo se llama como ese export.
- Test al lado: `formatPrice.ts` + `formatPrice.test.ts`.
- Sin `index.ts` que re-exporta todo. Ver [[Folder Structure]].

---

## Enlaces

- [[Coding Principles]] · [[Components]] · [[Services]] · [[Folder Structure]]
