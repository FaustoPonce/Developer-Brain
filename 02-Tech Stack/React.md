---
tags: [stack, frontend, react]
updated: 2026-07-26
status: draft
---

# React

> La base de todo mi front. Casi siempre dentro de [[NextJS]].

---

## Cómo escribo componentes

- Función nombrada, `export default` solo en páginas.
- Props tipadas con `type`, nunca `any`. Ver [[TypeScript]].
- Sin `React.FC`.
- Un componente por archivo. El archivo se llama como el componente.
- Orden interno siempre igual: hooks → valores derivados → handlers → early returns → JSX.

Ver [[Components]] para la taxonomía y el orden interno en detalle.

---

## Hooks

- Custom hook cuando hay lógica con estado repetida o un `useEffect` que necesita explicación.
- Prefijo `use`, un solo propósito, retorna objeto si son 3+ valores.
- `useEffect` es último recurso: sincronizar con algo externo, no derivar estado.

Ver [[Hooks]].

---

## Estado

Escalera, en orden. No subo un escalón sin necesidad:

1. Variable derivada (no es estado).
2. `useState` local.
3. Levantar al padre.
4. Context (solo cosas que cambian poco: theme, user, locale).
5. Librería de estado global.

Server state ≠ client state. El server state vive en el fetch, no en `useState`.
Ver [[State Management]].

---

## Performance

Reglas, en este orden:

1. No re-renderizar de más → estado lo más abajo posible.
2. Listas con `key` estable (nunca el índice si el array se reordena).
3. `memo` / `useMemo` / `useCallback` **solo después de medir** con el Profiler.
4. Virtualizar listas de 100+ items.

---

## Accesibilidad (mínimo obligatorio)

- Elementos semánticos: `button` es `<button>`, no `<div onClick>`.
- Todo input con `<label>` asociado.
- Foco visible. Nunca `outline: none` sin reemplazo.
- Contraste AA.
- Imágenes con `alt` real o `alt=""` si son decorativas.

---

## Enlaces

- [[NextJS]] · [[TypeScript]] · [[Components]] · [[Hooks]] · [[State Management]]
