---
tags: [architecture, structure]
updated: 2026-07-26
status: draft
---

# Folder Structure

> Predecible antes que inteligente. Si tengo que buscar dónde va algo, la estructura falló.

---

## Principio

**Agrupo por feature, no por tipo de archivo.** Un cambio de negocio debería tocar
una carpeta, no seis.

La excepción es lo genuinamente compartido: `components/ui`, `lib`, `types`.

---

## Next.js App Router

```
src/
├── app/
│   ├── (marketing)/            # Route groups por audiencia
│   │   ├── page.tsx
│   │   └── layout.tsx
│   ├── (app)/
│   │   └── dashboard/page.tsx
│   ├── api/
│   │   └── webhooks/route.ts
│   ├── layout.tsx
│   ├── sitemap.ts
│   └── robots.ts
│
├── features/                   # El corazón
│   └── games/
│       ├── components/
│       ├── hooks/
│       ├── services/
│       ├── schemas/
│       └── types.ts
│
├── components/
│   ├── ui/                     # Primitivos: Button, Input, Dialog
│   └── layout/                 # Header, Footer, Nav
│
├── lib/                        # Clientes externos + utils puras
├── config/                     # env, constantes, feature flags
├── hooks/                      # Hooks globales de verdad
├── types/                      # Tipos compartidos
└── styles/
```

**`app/` solo enruta y compone.** La lógica vive en `features/`.

### Cuándo NO hay `features/`

La estructura de arriba es para una app con **varias** áreas de negocio distintas
(dashboard, facturación, ajustes). Un sitio de contenido no es eso: tiene **una** entidad
que se repite en todas las páginas.

Ahí `features/` es una carpeta con un solo hijo, o sea ruido. La estructura correcta es
plana y por rol técnico:

```
src/
├── app/            rutas
├── config/         identidad del sitio, en un solo lugar
├── i18n/           idiomas, mapa de rutas, diccionarios
├── lib/            acceso a datos, SEO, helpers
├── types/          forma de la entidad
├── data/           el JSON versionado
└── components/
```

Es la estructura de la plantilla. **No es una excepción a la regla, es la regla aplicada:**
agrupar por feature cuando hay features, y no inventar la carpeta cuando hay una sola.
Ver [[Coding Principles]] → regla de tres.

Cuando el sitio de contenido crece a dos entidades no relacionadas, ahí sí aparece
`features/`.

---

## Backend ([[Node]])

```
src/
├── routes/
├── services/
├── repositories/
├── schemas/
├── lib/
├── config/
└── index.ts
```

Ver [[Clean Architecture]] para la dirección de las dependencias.

---

## Reglas

1. **Colocación:** si algo lo usa una sola feature, vive dentro de esa feature.
2. **Promoción tardía:** se mueve a `components/` o `lib/` recién cuando lo usa una **segunda** feature. Regla de tres, ver [[Coding Principles]].
3. **Sin `utils.ts` bolsa de gatos.** Nombre por dominio: `formatPrice.ts`, `slugify.ts`.
4. **Sin carpetas de un archivo** "por si crece".
5. **Sin barrel files** (`index.ts` que re-exporta todo) — rompen tree-shaking y hacen ciclos.
6. **Imports absolutos** con `@/`. Relativos solo dentro de la misma carpeta.
7. **Máximo 3 niveles** de anidación dentro de una feature.

---

## Raíz del repo

```
.
├── src/
├── public/
├── docs/               # ADRs, notas del proyecto
├── scripts/
├── .env.example
├── README.md           # Cómo levantarlo. Sin adornos.
└── AGENTS.md           # Contexto para agentes de IA
```

---

## Enlaces

- [[Clean Architecture]] · [[Naming]] · [[New Project]] · [[NextJS]]
