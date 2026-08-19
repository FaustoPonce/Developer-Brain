---
tags: [stack, frontend, framework, nextjs]
updated: 2026-08-18
status: active
---

# Next.js

> Mi default para cualquier cosa con cara de web. App Router.

---

## Por qué

- SSR/SSG de fábrica → **SEO real**, que es de donde viene mi tráfico.
- Un solo repo: front + API routes. Sin coordinar dos deploys.
- Deploy trivial en Vercel o Cloudflare Pages.
- Lo conozco. Ver [[Decision Making]] → criterio #1.

**Cuándo NO:** app 100% interna sin SEO y sin necesidad de SSR → Vite + React alcanza y arranca más rápido.

---

## Decisiones fijas

| Tema | Decisión |
|---|---|
| Router | App Router. Pages Router solo en repos legacy. |
| Componentes | Server Components por default. `"use client"` solo cuando hace falta. |
| Data fetching | En Server Components, directo. Nada de `useEffect` para carga inicial. |
| Mutaciones | Server Actions para forms; Route Handlers para APIs públicas. |
| Estilos | Tailwind. Ver [[React]]. |
| Imágenes | `next/image` siempre. |
| Fuentes | `next/font` — evita CLS. |

---

## Versiones: 14 vs 16 conviven

Tengo proyectos en las dos y **hay un cambio que rompe silenciosamente**:

> **Desde Next 15, `params` y `searchParams` son promesas.** Hay que `await`-earlos.
> En 14 son objetos planos.

```tsx
// ❌ Next 14. En 16 el tipo miente y el valor es undefined en runtime.
function Page({ params }: { params: { lang: string } }) { const l = params.lang; }

// ✅ Next 16
async function Page({ params }: { params: Promise<{ lang: string }> }) {
  const { lang } = await params;
}
```

Por qué importa: al copiar una página de un proyecto viejo a uno nuevo, el tipo compila
si se declara mal y el fallo aparece como valores vacíos, no como error.

**Antes de escribir una página, mirar la versión en `package.json`.**
Proyectos nuevos: Next 16 (es lo que usa la plantilla).

---

## Reglas de Server vs Client

Marco `"use client"` **solo** si el componente usa: `useState`, `useEffect`, event handlers,
APIs del browser o context.

Empujar `"use client"` lo más abajo posible en el árbol. Un client component en el layout
raíz convierte toda la app en cliente.

```tsx
// ❌ page.tsx entera como cliente por un botón
"use client";

// ✅ page.tsx server, y el botón aislado
// components/CopyButton.tsx → "use client"
```

---

## SEO — no negociable

Cada página pública necesita:

- `generateMetadata` con `title`, `description`, `openGraph`, `alternates.canonical`.
- Canonical **absoluta** y única. Duplicados = deindexación.
- `hreflang` correcto si hay i18n (y `x-default`).
- `sitemap.ts` y `robots.ts` generados, no estáticos a mano.
- JSON-LD donde aplique (`Article`, `FAQPage`, `SoftwareApplication`).
- Sin `noindex` accidental en producción.

Ver checklist en `09-Checklists/`.

---

## Performance

- `dynamic()` para cargas pesadas debajo del fold.
- `revalidate` explícito en cada fetch. Nada de cache por accidente.
- `loading.tsx` + Suspense en vez de spinners manuales.
- Objetivo: LCP < 2.5s, CLS < 0.1 en móvil.

---

## Errores que ya cometí

| Error | Consecuencia |
|---|---|
| Ruta traducida que hereda el idioma del segmento padre | ~800 URLs duplicadas por producto cartesiano |
| Canonical construida desde el parámetro de la URL | Duplicados sin versión canónica |
| `lastmod` con la fecha del build | Google descarta la señal del sitemap |
| Podar contenido por antigüedad | 404 recurrentes sobre URLs indexadas |
| `"use client"` en el layout | Todo el bundle al cliente |
| `disallow: ['/_next/']` en `robots.ts` | Google no puede renderizar la página con sus JS/CSS/fuentes reales |
| Atributo puesto a mano en `<html>` (`data-theme`, etc.) con layout raíz `[lang]/layout.tsx` | Se pierde silenciosamente al cambiar de idioma — ver abajo |
| `trailingSlash: true` asumido como propagado a todo | `<Link>` solo lo normaliza en rutas 100% dinámicas — `sitemap.ts`, `llms.ts`, JSON-LD y `<a>` planas quedan sin `/` final y el hosting las 307-redirige |
| `not-found.tsx` puesto dentro de `[lang]/` en vez de la raíz de `app/`, en un proyecto con `output: 'export'` | Nunca se sirve — `out/404.html` sale solo del `not-found.tsx` de la raíz literal. `next dev` no lo detecta (ahí sí lo resuelve). Ver abajo |

Los post-mortems completos están en `07-Lessons Learned/`, y la checklist que los
previene en [[SEO Launch]].

---

## Layout raíz con segmento dinámico: `<html>` se recrea al navegar

Patrón usado en toda mi plantilla bilingüe: `[lang]/layout.tsx` es el layout
**raíz** (no hay `app/layout.tsx` arriba) y renderiza `<html lang={locale}>` directo,
porque es el único que conoce el idioma.

**El problema:** cuando `[lang]` cambia vía `<Link>` (el switch de idioma), Next.js
recrea el nodo `<html>` en el cliente. No es un reload completo — el `window`,
`localStorage` y el resto del JS siguen vivos — pero es un remount real del árbol
del layout raíz, así que **cualquier atributo puesto de forma imperativa sobre
`<html>`/`<body>` (fuera del render de React) se pierde**, sin warning ni error.
Un caso real: un switch de tema claro/oscuro manual que guarda la preferencia en
`data-theme` vía JS — al cambiar de idioma, el atributo desaparecía y la página caía
al `prefers-color-scheme` del sistema, ignorando lo que el usuario había elegido.

El script `beforeInteractive` de `next/script` (el que evita el flash del tema
incorrecto en la carga inicial) **no vuelve a correr** en ese remount — solo corre
una vez, en la carga real de documento.

**Fix:** cualquier estado que viva como atributo imperativo del DOM en `<html>`/`<body>`
necesita un `useLayoutEffect` **sin array de dependencias** (corre en cada render, no
solo al montar) en un componente client siempre presente en el layout, que reaplique
el valor desde la fuente real (`localStorage`, `prefers-color-scheme`, etc.) de forma
idempotente. Cubre tanto el primer montaje como cualquier remount posterior por cambio
de idioma — no hace falta detectar el remount explícitamente, alcanza con que el efecto
sea barato y no-op cuando el valor ya es correcto.

Aplica a cualquier proyecto de la plantilla que guarde preferencia de usuario
(tema, u otro) fuera del árbol de React sobre el layout raíz.

---

## `output: 'export'` + `[lang]/layout.tsx` como raíz: la 404 propia va en `app/`, no en `[lang]/`

Con `[lang]/layout.tsx` actuando de layout raíz (mismo patrón que la sección
anterior), es intuitivo poner `not-found.tsx` al lado, dentro de `[lang]/`. Anda en
`next dev` porque ahí Next resuelve `notFound()` en tiempo de request. En un export
estático no hay tiempo de request: `out/404.html` (lo que sirve cualquier hosting
para una URL sin archivo) sale **solo** de `app/not-found.tsx` en la raíz literal —
un `not-found.tsx` anidado en un segmento dinámico nunca se compila a ese archivo.

**Fix:** `app/not-found.tsx` (raíz) + `app/layout.tsx` (raíz, mínimo). Esto último
exige la misma pregunta que la sección anterior: ¿duplica el `<html>` de
`[lang]/layout.tsx`? No — confirmado con un build real. Next resuelve la
declaración `<html>` más profunda del árbol por ruta; el layout raíz nuevo solo
termina aplicándose a la página de 404 (la única sin un `[lang]/layout.tsx` encima).
El `lang` del layout raíz queda fijo (no puede saber el idioma real de una URL que
no matcheó nada) — aceptable, es la única página del sitio así.

**Se verifica sirviendo la página, no leyendo el código:** `curl` a una URL rota
real, o levantar el build exportado con un server estático y mirar el HTML. Ningún
`typecheck`/`lint`/`build` lo detecta. Ver
[[2026-08-18 - not-found.tsx fuera de la raiz nunca se sirve en export estatico]].

---

## Generación estática a escala

La mayoría de mis sitios son exportación estática con miles de páginas. Lo que importa ahí:

- **Los parámetros estáticos son el contrato de qué URLs existen.** Lo que se emita de
  más, existe, devuelve 200 y Google lo encuentra. Ver [[Internationalization]].
- Sin servidor no hay redirecciones ni middleware en runtime: **todo se resuelve en
  build o en la capa de hosting**. Decidirlo antes de elegir exportación estática.
- El contenido vive como datos versionados en el repo, no en una base.
  Ver [[Content Pipelines]].
- Ver [[Programmatic SEO]] antes de generar la primera página en masa.

---

## Enlaces

- [[React]] · [[TypeScript]] · [[Deployment]] · [[API Design]]
