---
tags: [stack, backend, dotnet, csharp]
updated: 2026-07-26
status: draft
---

# ASP.NET Core

> Cuando el backend necesita tipado fuerte, performance y lógica de negocio pesada.

---

## Cuándo lo elijo sobre [[Node]]

- Dominio complejo con muchas reglas → el sistema de tipos de C# me cubre más.
- Trabajo pesado de CPU o concurrencia alta.
- Cliente/contexto que ya vive en el ecosistema .NET.

**Cuándo no:** proyecto chico donde el front ya es [[NextJS]] — un solo lenguaje gana.

---

## Decisiones fijas

| Tema | Decisión |
|---|---|
| Versión | LTS actual. |
| API | Minimal APIs para servicios chicos, Controllers cuando hay muchos endpoints. |
| ORM | EF Core. Dapper solo para queries calientes. |
| Validación | FluentValidation o Data Annotations. Ver [[Validation]]. |
| Nullable | `<Nullable>enable</Nullable>` siempre. |
| Mapeo | Manual o Mapperly. Nada de AutoMapper mágico. |

---

## Estructura por capas

```
src/
├── Api/              # Endpoints, middleware, DI
├── Application/      # Casos de uso, DTOs, interfaces
├── Domain/           # Entidades, value objects, reglas
└── Infrastructure/   # EF Core, servicios externos
```

La dependencia apunta hacia adentro: `Api → Application → Domain`.
`Infrastructure` implementa interfaces que declara `Application`. Ver [[Clean Architecture]].

---

## Reglas

1. **Inyección de dependencias** por constructor, siempre. Nada de service locator.
2. **`async` de punta a punta.** Nada de `.Result` ni `.Wait()` — deadlock garantizado.
3. **`CancellationToken`** propagado en toda operación async.
4. **DTOs en el borde.** Las entidades de dominio no cruzan la capa HTTP.
5. **Scopes de DI correctos:** `DbContext` es `Scoped`. `Singleton` con estado mutable es un bug.

---

## Errores y respuestas

- Middleware global de excepciones → devuelve `ProblemDetails` (RFC 7807).
- Nada de `try/catch` por endpoint para lo genérico.
- Errores de negocio esperados → resultado tipado, no excepción.

Ver [[Error Handling]] y [[API Design]].

---

## EF Core

- Migraciones versionadas en el repo, revisadas antes de aplicar.
- `AsNoTracking()` en todas las lecturas.
- Ojo con N+1: `Include` explícito o proyección con `Select`.
- Proyectar a DTO en la query, no traer la entidad entera.
- Loguear SQL generado en desarrollo.

---

## Enlaces

- [[Clean Architecture]] · [[PostgreSQL]] · [[Docker]] · [[API Design]] · [[Repositories]]
