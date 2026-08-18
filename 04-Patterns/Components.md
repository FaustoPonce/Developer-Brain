---
tags: [patterns, react, components]
updated: 2026-08-16
status: draft
---

# Components

> Un componente tiene una responsabilidad. Si el nombre lleva "y", son dos.

---

## Taxonomía

| Tipo | Vive en | Sabe de negocio | Ejemplo |
|---|---|---|---|
| **UI / primitivo** | `components/ui/` | No | `Button`, `Input`, `Dialog` |
| **Layout** | `components/layout/` | No | `Header`, `Sidebar`, `Container` |
| **Feature** | `features/x/components/` | Sí | `GameCard`, `PriceHistory` |
| **Page** | `app/**/page.tsx` | Compone | `GamesPage` |

Un componente de `ui/` que importa algo de `features/` está mal orientado.

---

## Anatomía

Todos mis componentes se leen en el mismo orden. Sin excepciones — la predecibilidad
es el punto:

1. **Tipo `Props`**, arriba del componente, en el mismo archivo.
2. **Hooks**, todos juntos. Nunca condicionales.
3. **Valores derivados** — lo que se calcula a partir de props y estado.
4. **Handlers**, nombrados `handleAlgo`.
5. **Early returns** para casos borde y estados especiales.
6. **JSX** del caso feliz, al final.

Cuando el orden se respeta, ubicar cualquier cosa en un componente ajeno lleva segundos.
Cuando no, hay que leerlo entero.

---

## Reglas

1. **Props explícitas.** Nada de `{...rest}` esparcido salvo en primitivos de `ui/`.
2. **Máximo ~6 props.** Más que eso: pasar un objeto o partir el componente.
3. **Sin booleanos que se excluyen** (`isPrimary` + `isSecondary`) → `variant`.
4. **Composición sobre configuración.** Un `Card` con `children` gana a un `Card` con 12 props.
5. **Un solo `return` de JSX principal**, más early returns para casos borde.
6. **Sin fetch dentro de componentes de presentación.** Los datos bajan por props o vienen del Server Component padre.
7. **`key` estable** en listas — id, nunca índice si el array puede reordenarse.

---

## Composición: el patrón que más uso

```tsx
// ❌ configuración: cada caso nuevo agrega una prop
<Modal title="..." body="..." confirmText="..." showIcon danger />

// ✅ composición: los casos nuevos no tocan el Modal
<Modal><Modal.Header/><Modal.Body/><Modal.Footer/></Modal>
```

La señal de alarma es agregar una prop al componente cada vez que aparece un caso de uso.
Eso quiere decir que el componente está decidiendo cosas que debería recibir.

---

## Estados que todo componente de datos necesita

Cuatro, siempre. Olvidar uno es el bug de UI más común:

1. **Loading** — skeleton, no spinner (evita CLS).
2. **Empty** — con texto útil y una acción.
3. **Error** — mensaje + reintentar. Ver [[Error Handling]].
4. **Success** — el caso feliz.

---

## Chrome persistente (header, footer, nav)

Se escribe una vez, al principio del proyecto, y después casi nunca se vuelve a
tocar visualmente — cada sesión posterior edita contenido o datos, no chrome. Por
eso es donde más fácil se pudre un patrón responsive que sí se respeta en el resto
del sitio: nadie lo revisita hasta que algo lo rompe.

- **`flex-wrap` o un breakpoint explícito, siempre.** Una fila `justify-between` sin
  ninguno de los dos asume que el contenido nunca va a crecer — un string más largo
  en una traducción, un botón nuevo, y rompe en silencio.
- Se prueba a los anchos mínimos reales (320px en adelante), no angostando la
  ventana del navegador a ojo. Ningún chequeo automático (typecheck, lint, build)
  detecta overflow horizontal — es el único bug de esta lista que solo aparece
  mirando el layout renderizado.
  Ver [[2026-08-16 - Header compartido sin breakpoint mobile pasa cualquier chequeo automatico]].

## Enlaces

- [[React]] · [[Hooks]] · [[Naming]] · [[Folder Structure]]
