---
tags: [architecture, layers, design]
updated: 2026-07-26
status: draft
---

# Clean Architecture

> Me quedo con la idea, no con la ceremonia: **las dependencias apuntan hacia adentro**.

---

## Lo que sí adopto

Tres capas, no cuatro con 20 interfaces:

```
┌─────────────────────────────────────┐
│  Delivery   route / controller / UI │  ← HTTP, React, CLI
├─────────────────────────────────────┤
│  Application  services / use cases  │  ← reglas de negocio
├─────────────────────────────────────┤
│  Data       repositories / clients  │  ← DB, APIs externas
└─────────────────────────────────────┘
```

**Regla única:** la capa de negocio no importa nada de HTTP ni de SQL.
Si `services/` sabe qué es un `Request` o un `SELECT`, la capa se filtró.

---

## Responsabilidades

| Capa | Hace | No hace |
|---|---|---|
| **Delivery** | Parsear input, validar, status codes, render | Lógica de negocio |
| **Application** | Reglas, orquestación, transacciones | Saber de HTTP o del ORM |
| **Data** | Queries, llamadas a APIs, mapeo a dominio | Decidir reglas de negocio |

Ver [[Services]] y [[Repositories]].

---

## El recorrido de una petición

Siempre el mismo, en las tres capas:

1. **Delivery** valida el input contra un schema, llama al service y traduce el resultado
   a un status code. Nada más. Si tiene un `if` de negocio, está mal.
2. **Application** carga lo que necesita, aplica las reglas, decide, y devuelve un
   resultado tipado. No sabe que existe HTTP.
3. **Data** ejecuta la query y devuelve tipos de dominio.

Las reglas de negocio se extraen a **funciones puras** (cálculos, permisos, validaciones
de estado) que no tocan ni la red ni la DB. Esas son las que testeo.

**Prueba rápida de que las capas están bien:** buscar `req`, `res` o SQL en `services/`.
Si aparecen, la capa se filtró.

---

## Lo que NO adopto

- Una interfaz por cada clase "por si cambio de implementación". Se agrega cuando **realmente** hay dos.
- Entidades de dominio anémicas envueltas en 4 mappers.
- Carpeta `UseCases/` con una clase de un método por acción — en TS una función alcanza.
- DTOs duplicados en cada capa cuando el shape es idéntico.

Esto es [[Development Philosophy]] → "complejidad solo cuando duele".

---

## Cuándo simplifico aún más

Proyecto chico (una landing, un tool con 3 endpoints): **dos capas**.
Route → service. El repositorio aparece cuando la query se usa desde dos lugares.

Escalar la arquitectura es barato. Desarmar la de más, no.

---

## Enlaces

- [[Folder Structure]] · [[Services]] · [[Repositories]] · [[Error Handling]] · [[ASP.NET]]
