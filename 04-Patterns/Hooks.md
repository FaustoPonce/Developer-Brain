---
tags: [patterns, react, hooks]
updated: 2026-07-26
status: draft
---

# Hooks

> Un custom hook existe para **sacar complejidad** de un componente, no para ordenar archivos.

---

## Cuándo creo uno

- La misma lógica con estado aparece en 2+ componentes (regla de tres flexible acá).
- Un `useEffect` necesita comentario para entenderse.
- El componente tiene 3+ `useState` relacionados entre sí.
- Hay que envolver una API del browser (`localStorage`, `matchMedia`, `IntersectionObserver`).

**Cuándo NO:** lógica pura sin estado → eso es una función en `lib/`, no un hook.

---

## Convenciones

- Prefijo `use`, nombre que dice **qué da**, no cómo: `useGameFilters`, no `useGameLogic`.
- Un archivo por hook, mismo nombre.
- Retorna objeto si son 3+ valores; tupla solo si es claramente `[valor, setter]`.
- Tipa el retorno si el hook es exportado fuera de su feature.
- Genérico si el hook no necesita saber el tipo del dato que maneja.
- Todo lo que el hook suscribe, agenda o registra, lo desarma en el cleanup.

---

## useEffect: último recurso

Un efecto **sincroniza con algo externo a React**. Nada más.

| No lo uso para | Uso en su lugar |
|---|---|
| Derivar estado de props | Cálculo en el render |
| Transformar datos para mostrar | Variable derivada |
| Reaccionar a un click | El handler del evento |
| Fetch inicial en [[NextJS]] | Server Component |
| Resetear estado al cambiar prop | `key` en el componente |

Sí lo uso para: suscripciones, timers, event listeners del `window`, sincronizar con
`localStorage`, integrar librerías no-React.

**Siempre con cleanup.** Un listener sin `return () => removeEventListener(...)` es un leak.

---

## Reglas de dependencias

- El array de deps se respeta. No se desactiva el lint rule.
- Si el efecto corre de más por una función en deps → `useCallback` o mover la función adentro.
- Si un objeto/array en deps rompe todo → memoizarlo o depender de sus campos primitivos.

---

## Hooks que suelo tener en cada proyecto

- `useDebounced` — inputs de búsqueda
- `useMediaQuery` — breakpoints en JS
- `useLocalStorage` — con guard de SSR
- `useClickOutside` — dropdowns y modales
- `useCopyToClipboard` — con estado de feedback

Viven en `hooks/` global solo si los usan varias features. Ver [[Folder Structure]].

---

## Enlaces

- [[React]] · [[Components]] · [[State Management]]
