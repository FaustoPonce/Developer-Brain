---
tags: [workflow, deploy, infra]
updated: 2026-07-26
status: draft
---

# Deployment

> Deployar tiene que ser aburrido. Si da miedo, hay algo mal en el proceso.

---

## Dónde deployo qué

| Tipo | Plataforma | Por qué |
|---|---|---|
| Next.js con SEO | Vercel / Cloudflare Pages | Build automático, edge, free tier |
| Sitio estático | Cloudflare Pages | Rápido y gratis |
| API [[Node]] | Railway / Fly.io / VPS | Procesos largos, cron |
| API [[ASP.NET]] | Container en VPS o Azure | Ver [[Docker]] |
| [[PostgreSQL]] | Neon / Supabase | Backups gestionados |
| DNS | Cloudflare | Ya tengo los dominios ahí |

---

## Principios

1. **Deployar temprano y seguido.** Deploys chicos = rollbacks fáciles.
2. **Un solo comando o un push.** Si son 7 pasos manuales, en el paso 5 me equivoco.
3. **Producción se toca solo desde `main`.** Ver [[Git]].
4. **Rollback conocido antes de deployar.** Si no sé cómo volver atrás, no deployo.
5. **Migraciones de DB en un deploy aparte** del código que las usa. Ver [[PostgreSQL]].
6. **Verificar en producción, siempre.** "Compiló" no es "funciona".

---

## Antes de deployar

- [ ] `npm run build` pasa en local
- [ ] Typecheck y lint sin errores
- [ ] Tests verdes
- [ ] Env vars nuevas cargadas **en el proveedor**, no solo en `.env.local`
- [ ] Migraciones aplicadas y probadas
- [ ] Sin `console.log` de debug ni código comentado
- [ ] Sin secrets en código de cliente

---

## Después de deployar (5 minutos, no negociables)

- [ ] Abrir el sitio en producción. En serio, abrirlo.
- [ ] Probar el flujo crítico (el que genera plata).
- [ ] Consola del browser sin errores.
- [ ] Logs del servidor sin picos.
- [ ] Móvil real.
- [ ] Si hay dominio nuevo: `www` y apex resuelven a lo mismo.

---

## Cuando algo se rompe en prod

1. **Rollback primero, investigar después.** Restaurar el servicio no es rendirse.
2. Reproducir en local con los datos que lo rompieron.
3. Arreglar, testear, redeployar.
4. Post-mortem en `07-Lessons Learned/` si afectó usuarios.

---

## Dominios y DNS

- Un solo dominio canónico. El otro redirige 301.
- HTTPS forzado.
- Propagación de DNS: hasta 48h. No entrar en pánico a los 10 minutos.
- Renovación automática activada. Perder un dominio indexado es irreversible.

---

## Notas por proyecto

Los comandos específicos de cada proyecto van en el `README.md` **de ese repo**,
no acá. Esta nota es el proceso; el repo tiene los comandos.

---

## Enlaces

- [[New Project]] · [[Git]] · [[Docker]] · [[Debugging]]
