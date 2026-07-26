---
tags: [identity, principles, code]
updated: 2026-07-26
status: draft
---

# Coding Principles

> Reglas duras. Si rompo una, tiene que haber un comentario explicando por qué.

---

## Reglas no negociables

1. **Explícito > implícito.** Nada de magia. Si hay que buscar de dónde sale algo, está mal.
2. **Funciones que hacen una cosa.** Si el nombre necesita un "y", son dos funciones.
3. **Fallar temprano y fuerte.** Validar en el borde del sistema, no en el medio.
4. **Sin estado global mutable.** Si necesito compartir estado, es explícito. Ver [[State Management]].
5. **Tipar los bordes.** API responses, forms, env vars, params de URL. Ver [[Validation]].
6. **Nada de `any`.** Si no sé el tipo, es `unknown` y lo valido. Ver [[TypeScript]].
7. **Errores no se tragan.** Un `catch` vacío es un bug futuro. Ver [[Error Handling]].
8. **Sin números mágicos.** Constante nombrada, arriba del archivo o en `constants.ts`.

---

## Sobre el tamaño

- **Función:** si no entra en una pantalla, se parte.
- **Componente:** si tiene más de un `useEffect` complicado, sale un hook. Ver [[Hooks]].
- **Archivo:** ~200 líneas es señal de alerta, no regla.
- **PR / commit:** una intención por commit. Ver [[Git]].

---

## Duplicación vs abstracción

> Regla de tres.

| Veces que aparece | Qué hago |
|---|---|
| 1 | Lo escribo inline. |
| 2 | Lo duplico. Todavía no sé cuál es el patrón real. |
| 3 | Recién ahí abstraigo, y ya sé qué varía y qué no. |

La duplicación es más barata que la abstracción equivocada.

---

## Comentarios

- **No** comentar *qué* hace el código. Eso lo dice el código.
- **Sí** comentar *por qué* se hizo así, sobre todo si parece raro.
- **Sí** dejar `// HACK:` / `// TODO:` con contexto, nunca sueltos.

```ts
// Stripe manda el amount en centavos pero Paddle en unidades.
// No unificar acá: el mapeo vive en el adapter de cada gateway.
const amount = provider === "paddle" ? raw : raw / 100;
```

---

## Testing

Testeo por riesgo, no por cobertura.

**Siempre:**
- Lógica de negocio pura (cálculos, precios, fechas, permisos).
- Bugs que ya me pasaron una vez (test de regresión). Ver `07-Lessons Learned/`.
- Parsers y transformaciones de datos externos.

**Casi nunca:**
- Componentes que solo renderizan props.
- Wrappers finos sobre librerías.
- Getters/setters.

---

## Enlaces

- [[Development Philosophy]]
- [[Naming]]
- [[Error Handling]]
- [[Validation]]
