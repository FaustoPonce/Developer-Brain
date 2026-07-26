---
tags: [stack, language, typescript]
updated: 2026-07-26
status: draft
---

# TypeScript

> Siempre. En todos los proyectos nuevos, sin excepción.

---

## Config base

> **Plantilla.** Se copia tal cual en un proyecto nuevo. No es un ejemplo.

```json
{
  "compilerOptions": {
    "strict": true,
    "noUncheckedIndexedAccess": true,
    "noImplicitOverride": true,
    "noFallthroughCasesInSwitch": true,
    "verbatimModuleSyntax": true,
    "target": "ES2022",
    "moduleResolution": "bundler",
    "skipLibCheck": true,
    "paths": { "@/*": ["./src/*"] }
  }
}
```

`strict: true` desde el día 1. Activarlo después es un proyecto entero.

---

## Reglas

1. **Cero `any`.** Si no sé el tipo → `unknown` + validación. Ver [[Validation]].
2. **`type` por default**, `interface` solo si necesito declaration merging o `extends` de clases.
3. **No anotar lo que se infiere.** Sí anotar el retorno de funciones exportadas.
4. **Union de literales > enum.** `type Status = "idle" | "loading" | "error"`.
5. **`as const`** para objetos de configuración y arrays fijos.
6. **`satisfies`** cuando quiero chequear la forma sin perder el tipo literal.
7. **Casteos (`as`) son deuda.** Cada uno necesita comentario que lo justifique.

---

## Patrones que uso

**Result en vez de excepciones** para errores esperados:

```ts
type Result<T, E = Error> =
  | { ok: true; data: T }
  | { ok: false; error: E };
```

Ver [[Error Handling]].

**Discriminated unions** para estados — hace imposible el estado inválido:

```ts
type RequestState<T> =
  | { status: "idle" }
  | { status: "loading" }
  | { status: "success"; data: T }
  | { status: "error"; message: string };
```

**Branded types** para IDs que no se deben mezclar:

```ts
type UserId = string & { readonly __brand: "UserId" };
```

**Exhaustividad** en switches:

```ts
function assertNever(x: never): never {
  throw new Error(`Caso no manejado: ${JSON.stringify(x)}`);
}
```

---

## Dónde vive cada tipo

- Tipos de dominio → `src/types/` o junto al módulo que los posee.
- Tipos de API → derivados del schema de validación (`z.infer`), nunca escritos dos veces.
- Props de componentes → en el mismo archivo del componente.

Una sola fuente de verdad por tipo. Si escribo el mismo shape dos veces, uno de los dos se va a desincronizar.

---

## Enlaces

- [[React]] · [[Node]] · [[Validation]] · [[Coding Principles]]
