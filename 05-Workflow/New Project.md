---
tags: [workflow, setup, process]
updated: 2026-07-26
status: active
---

# New Project

> De la idea a algo deployado. Sin improvisar el setup cada vez.

---

## Fase 0 — Antes de escribir código (30 min máx.)

- [ ] **Una frase:** qué hace y para quién.
- [ ] **Cómo hace plata** (o por qué no hace falta): AdSense, suscripción, lead, portfolio.
- [ ] **De dónde viene el tráfico:** SEO, redes, directo. Si es SEO → decide el stack.
- [ ] **Alcance del MVP:** 3 features. Lo demás va a una lista de "después".
- [ ] **Dominio** disponible y comprado.
- [ ] Decisiones de una vía anotadas. Ver [[Decision Making]].

No sigo hasta tener esto. Es la diferencia entre un producto y una carpeta más.

---

## Dos caminos

| El proyecto es… | Ir a |
|---|---|
| Sitio estático bilingüe con SEO | **Fase 1a** — sale de la plantilla |
| Cualquier otra cosa | **Fase 1b** — andamiaje a mano |

---

## Fase 1a — Sitio estático bilingüe: lo hace la plantilla

**No se arma el andamiaje a mano.** El repo `Template proyectos/Pages-Template` ya trae
resuelto todo lo que antes eran tres fases: TypeScript `strict`, estructura de carpetas,
i18n con rutas traducidas, sitemap, robots, canonicals, 404, redirección de la raíz,
analytics por variable de entorno y la verificación automática de SEO.

El procedimiento concreto —qué archivo tocar, en qué orden— vive en el `SETUP.md` de
ese repo, porque son rutas y comandos de **ese** repo. Acá va el criterio, allá el paso
a paso. Ver la regla 3 del [[00-README|vault]]: nombres de archivos concretos no entran.

Lo que sí sigue siendo decisión mía, no de la plantilla:

- [ ] El **modelo de datos** de la entidad, y el criterio de la puerta de calidad:
      qué hace que una página esté lista para publicarse. Ver [[Programmatic SEO]].
- [ ] Las **rutas** que va a tener el sitio, antes de crear carpetas.
- [ ] **Contenido real** en la primera página. Nada de lorem ipsum.
- [ ] **Deploy a producción vacío**, antes de tener features. Deployar temprano evita
      descubrir problemas de build con 40 archivos encima.
- [ ] Search Console verificado y sitemap enviado.

---

## Fase 1b — Setup a mano, cuando no hay plantilla

La plantilla cubre **un** arquetipo: sitio estático bilingüe con SEO programático.
Un SaaS con base de datos y login, una API, una app interna sin SEO — nada de eso sale
de ahí y hay que armarlo.

Esta lista es lo que **toda** app nueva necesita, sin importar el stack. Es corta a
propósito: son los que si faltan, después cuestan diez veces más.

- [ ] **Tipado estricto desde el commit 1.** Activarlo después es un proyecto entero.
      Ver [[TypeScript]].
- [ ] **Linter y formateador** configurados y corriendo. Antes del primer feature, no después.
- [ ] **Un solo comando que verifique todo** (`check`): typecheck + lint + build + tests.
      Si verificar requiere recordar cuatro comandos, no se verifica.
- [ ] **Env vars validadas al arranque**, en un solo módulo, y el proceso no levanta si
      falta una. Nunca leer variables de entorno sueltas por el código. Ver [[Validation]].
- [ ] **`.env.example` commiteado**, `.env` ignorado.
- [ ] **Versión de runtime fijada** (`.nvmrc`, `engines`, o el equivalente).
- [ ] **Estructura de carpetas** por feature, decidida antes del primer archivo.
      Ver [[Folder Structure]].
- [ ] **Imports absolutos** configurados.
- [ ] **Tokens de diseño** (colores, tipografía, espaciado, modo oscuro) en un solo lugar,
      antes de escribir el segundo componente.
- [ ] **`README.md`**: qué es, cómo se levanta, cómo se deploya. Tres secciones, nada más.
- [ ] **`CLAUDE.md`**: apunta al vault y documenta lo propio del repo. Ver la plantilla
      de la sección final del `CLAUDE.md` del vault.
- [ ] **Repo remoto creado y pusheado vacío**, antes de tener código.
- [ ] **Deploy a producción vacío**, antes de tener features.
- [ ] Si hay base de datos: **backups activados y una restauración probada.**
      Ver [[PostgreSQL]].
- [ ] Si es público: la checklist de [[SEO Launch]] aunque el SEO no sea el modelo.

Cuando un arquetipo nuevo se repita **tres veces**, se convierte en su propia plantilla.
Hoy hay una; el próximo candidato es el SaaS con suscripción.

---

## Fase 4 — Features del MVP

Una feature a la vez, cada una hasta producción antes de arrancar la siguiente.
Feature "terminada" = deployada, verificada en prod y con los 4 estados de UI
(loading, empty, error, success). Ver [[Components]].

---

## Fase 5 — Antes de considerarlo lanzado

- [ ] Lighthouse móvil > 90 en Performance y SEO.
- [ ] Probado en móvil real, no solo en devtools.
- [ ] Página 404 propia.
- [ ] Todas las rutas funcionan en producción (no solo en local).
- [ ] Sin errores en la consola del browser.
- [ ] Sin claves ni secrets en el bundle del cliente.
- [ ] Backups activados si hay DB. Ver [[PostgreSQL]].

Checklist detallada en `09-Checklists/`.

---

## Enlaces

- [[Decision Making]] · [[Folder Structure]] · [[Deployment]] · [[Git]] · [[NextJS]]
