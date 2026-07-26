---
tags: [patterns, backend, data]
updated: 2026-07-26
status: draft
---

# Repositories

> El único lugar del código que sabe cómo están guardados los datos.

---

## Qué es

Un módulo que traduce entre **el dominio** y **el storage**. Recibe y devuelve tipos
de dominio; adentro hay SQL, ORM o llamadas a una API externa.

Beneficio real (no teórico): cuando la query se rompe o se pone lenta, sé exactamente
dónde mirar. Y los [[Services]] se testean sin base de datos.

---

## Forma

Un objeto (o módulo) con métodos por operación, agrupado por agregado.

Cada método hace tres cosas y ninguna más: **arma la query**, **la ejecuta**, y
**mapea el resultado a tipos de dominio**. Ese mapeo es la frontera: adentro del repo
hay filas, afuera hay entidades.

Los verbos son consistentes en todos mis repos: `findById`, `findBy<Campo>`, `list`,
`insert`, `update`, `delete`. `find*` puede devolver `null`. Ver [[Naming]].

---

## Reglas

1. **Sin lógica de negocio.** El repo no decide *si* algo se puede hacer, solo lo hace.
2. **Devuelve tipos de dominio**, no filas crudas del ORM. El mapeo vive acá.
3. **`null` para "no encontrado"**, no excepción. El service decide si eso es un error.
4. **Un repo por agregado**, no por tabla. `orderRepo` puede tocar `orders` y `order_items`.
5. **Paginación como parámetro**, nunca traer todo y filtrar en memoria.
6. **Nada de `SELECT *`.** Columnas explícitas → si agrego una columna sensible, no se filtra sola.
7. **La transacción la abre el service**, el repo la recibe como parámetro opcional.
8. **Solo queries parametrizadas.**

---

## Cuándo NO uso repositorios

Proyecto chico con 3 queries triviales: el service habla con el ORM directo.
El repo aparece cuando:

- La misma query se usa desde dos services.
- La query es compleja y merece nombre propio.
- Quiero testear el service sin DB.

Ver [[Clean Architecture]] → cuándo simplifico.

---

## Performance

- Ojo con N+1: cargar hijos en una sola query (`Include`, `with`, o un `IN`).
- `EXPLAIN ANALYZE` antes de dar por buena una query nueva. Ver [[PostgreSQL]].
- Batch para escrituras masivas; nunca un `INSERT` por item en un loop.
- Timeout en toda query.

---

## Enlaces

- [[Services]] · [[Clean Architecture]] · [[PostgreSQL]] · [[Naming]]
