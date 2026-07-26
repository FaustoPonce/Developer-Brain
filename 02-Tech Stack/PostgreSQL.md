---
tags: [stack, database, postgres]
updated: 2026-07-26
status: draft
---

# PostgreSQL

> Mi base de datos por default. Siempre. Hasta que un requisito me obligue a otra cosa.

---

## Por qué

- Relacional cuando lo necesito, `jsonb` cuando no.
- Full-text search incluido — me ahorra un Elasticsearch.
- Constraints reales: la integridad vive en la DB, no solo en la app.
- Está en todos lados y con free tier (Neon, Supabase, Railway).

**Cuándo no:** cache o datos efímeros → Redis. Key-value simple en edge → KV del proveedor.

---

## Reglas de esquema

1. **PK:** `uuid` (v7 si se puede, por ordenamiento) o `bigint identity`. Nunca `serial` nuevo.
2. **Nombres:** tablas en plural y `snake_case`, columnas en `snake_case`. Ver [[Naming]].
3. **Timestamps:** `created_at` y `updated_at` con `timestamptz`. **Nunca** `timestamp` sin zona.
4. **Plata:** `numeric(12,2)` o enteros en centavos. **Jamás** `float`.
5. **`NOT NULL` por default.** Nullable es una decisión que se justifica.
6. **FKs siempre**, con `ON DELETE` explícito.
7. **Enums:** tabla de lookup o `check` constraint. El tipo `enum` nativo es doloroso de migrar.
8. **Borrado lógico** (`deleted_at`) solo si el negocio lo pide, no "por las dudas".

---

## Índices

- Toda FK usada en joins o filtros lleva índice.
- Índice compuesto: el orden de columnas importa (más selectiva primero).
- Índice parcial para queries con filtro fijo: `WHERE deleted_at IS NULL`.
- `EXPLAIN ANALYZE` antes de agregar cualquier índice. Sin plan, no hay índice.
- Índice de más = escrituras más lentas. No es gratis.

---

## Queries

- Nunca concatenar SQL. Parámetros siempre — inyección SQL sigue siendo el #1.
- `SELECT` de columnas explícitas, no `*`.
- Paginación por keyset en listas grandes; `OFFSET` grande se degrada.
- Transacción cuando hay 2+ escrituras que deben ser atómicas.

---

## Migraciones

- Versionadas en el repo, una dirección (adelante). Rollback = nueva migración.
- **Nunca** destructivas en un solo paso. Para renombrar/borrar columna:
  1. Agregar la nueva, escribir en ambas.
  2. Backfill.
  3. Cambiar lecturas.
  4. Borrar la vieja (deploy aparte).
- Probar la migración contra una copia de producción antes de correrla.

---

## Operación

- Backups automáticos activados y **restaurados al menos una vez de prueba**.
  Un backup no probado no es un backup.
- Connection pooling (PgBouncer / pooler del proveedor) — serverless abre conexiones sin control.
- Alertas sobre queries lentas.

---

## Enlaces

- [[Node]] · [[ASP.NET]] · [[Repositories]] · [[Docker]]
