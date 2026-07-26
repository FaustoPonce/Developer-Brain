---
tags: [stack, infra, docker]
updated: 2026-07-26
status: draft
---

# Docker

> Para dependencias locales y para empaquetar servicios. No para todo.

---

## Cuándo lo uso

- **Sí:** levantar [[PostgreSQL]], Redis y demás dependencias en local (`docker compose`).
- **Sí:** empaquetar un servicio [[Node]] o [[ASP.NET]] que va a un VPS o container host.
- **No:** proyectos [[NextJS]] que deployo en Vercel / Cloudflare Pages. Es fricción sin beneficio.

---

## Reglas del Dockerfile

1. **Multi-stage** siempre: build en una etapa, runtime en otra mínima.
2. **Imagen base fijada** por versión (`node:22-alpine`), nunca `latest`.
3. **Capas ordenadas por frecuencia de cambio:** copiar lockfile e instalar deps antes del código fuente.
4. **Usuario no-root** en el runtime.
5. **`.dockerignore`** con `node_modules`, `.git`, `.env`, `dist`.
6. **Nada de secrets** en el Dockerfile ni en args de build. Van por env en runtime.
7. **`HEALTHCHECK`** definido.

> **Plantilla.** Punto de partida para copiar, no un ejemplo a leer.

```dockerfile
FROM node:22-alpine AS deps
WORKDIR /app
COPY package.json package-lock.json ./
RUN npm ci

FROM node:22-alpine AS build
WORKDIR /app
COPY --from=deps /app/node_modules ./node_modules
COPY . .
RUN npm run build

FROM node:22-alpine AS runtime
WORKDIR /app
ENV NODE_ENV=production
COPY --from=deps /app/node_modules ./node_modules
COPY --from=build /app/dist ./dist
USER node
EXPOSE 3000
HEALTHCHECK CMD wget -qO- http://localhost:3000/health || exit 1
CMD ["node", "dist/index.js"]
```

---

## Compose para desarrollo

- Un `docker-compose.yml` por repo que levanta **solo las dependencias**, no mi app.
  La app corre nativa: hot reload más rápido y debug directo.
- Volumen nombrado para la data de Postgres — si no, se pierde en cada `down`.
- Puertos no estándar si tengo varios proyectos abiertos (5433, 5434...).
- `.env.example` commiteado; `.env` en `.gitignore`.

---

## Errores que ya cometí

- `COPY . .` antes de instalar deps → cache inútil, builds de minutos.
- Sin volumen en Postgres → base vacía cada mañana.
- `latest` en la base → build que funcionaba deja de funcionar solo.

---

## Enlaces

- [[Node]] · [[ASP.NET]] · [[PostgreSQL]] · [[Deployment]]
