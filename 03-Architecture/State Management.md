---
tags: [architecture, state, frontend]
updated: 2026-08-01
status: draft
---

# State Management

> La mayoría de los problemas de estado son problemas de **ubicación** del estado.

---

## Primera pregunta: ¿es estado?

Antes de un `useState`, chequear:

- ¿Se puede **derivar** de otro estado o de props? → variable, no estado.
- ¿Viene del **servidor**? → server state, no client state.
- ¿Puede vivir en la **URL**? → search params.

El 60% de los `useState` que escribo de más caen en alguna de estas tres.

```tsx
// ❌ estado derivado
const [total, setTotal] = useState(0);
useEffect(() => setTotal(items.reduce(sum)), [items]);

// ✅
const total = items.reduce(sum, 0);
```

---

## La escalera

Subo un escalón solo cuando el anterior duele:

| # | Herramienta | Para |
|---|---|---|
| 1 | Variable derivada | Todo lo calculable |
| 2 | `useState` local | Estado de un componente |
| 3 | URL (`searchParams`) | Filtros, tabs, paginación, búsqueda |
| 4 | Levantar al padre | Dos hermanos que comparten |
| 5 | Context | Theme, user, locale — cambia poco |
| 6 | Zustand / Jotai | Estado global que cambia seguido |
| 7 | TanStack Query / SWR | Server state con cache |

---

## Estado en la URL — subutilizado

Filtros, orden, página y query de búsqueda **van en la URL**. Gratis obtengo:
compartir el link, botón atrás funcional, refresh sin perder contexto, y en [[NextJS]]
el server puede leerlo y renderizar directo.

```tsx
const params = useSearchParams();
const platform = params.get("platform") ?? "all";
```

**Excepción — exportación estática con SEO programático:** si la página es de las que
vive en el sitemap con **una** URL canónica (un hub, un directorio, una ficha), el
filtro/orden client-side **no puede ser server-readable vía `useSearchParams`**. Google
rastrea e indexa `?marca=anker` como una variante más de la misma página, y eso es la
misma fragmentación que [[Programmatic SEO]] ya prohíbe, entrando por otra puerta.

En ese caso leo el deep-link con `useEffect` + `window.location` en vez de
`useSearchParams`, para que el estado de filtro nunca pase por el render del servidor
ni quede asociado a la canonical:

```tsx
// ❌ server-readable → Google indexa la variante filtrada
const params = useSearchParams();

// ✅ solo cliente, la canonical no se entera de que existe
useEffect(() => {
  const params = new URLSearchParams(window.location.search);
  setBrand(params.get('marca') ?? 'all');
}, []);
```

El filtro client-side es comodidad de navegación, no reemplaza una página estática con
contenido propio ("docks con 2 HDMI" sigue siendo su propia URL con su propia razón de
existir) — son dos mecanismos distintos y no se pisan.

---

## Server state ≠ client state

Datos del servidor tienen cache, staleness, refetch y estados de carga.
Eso no es `useState`.

- **App Router:** fetch en Server Components, `revalidate` explícito. Alcanza en la mayoría de mis casos.
- **Mucha interactividad / datos en vivo:** TanStack Query.
- **Optimistic updates:** `useOptimistic` + Server Action.

Nunca copiar datos del servidor a `useState` "para poder editarlos" sin una razón —
ahí nace la desincronización.

---

## Context: cuándo sí

Solo para valores que **cambian poco** y necesitan muchos consumidores: theme, sesión, locale, i18n.

Anti-patrón: un `AppContext` gigante con todo adentro. Cada cambio re-renderiza la app entera.
Contexts chicos y separados, o Zustand.

---

## Formularios

Estado de form aparte del resto. React Hook Form + Zod, con el mismo schema
que valida el servidor. Ver [[Validation]].

---

## Enlaces

- [[React]] · [[NextJS]] · [[Hooks]] · [[Validation]]
