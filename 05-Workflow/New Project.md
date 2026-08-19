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

## Elegir el punto de partida

**El costo en tokens no es el criterio.** Clonar siempre gana: borrar archivos es casi
gratis, generarlos es lo caro. Lo que decide es si las **suposiciones heredadas** ayudan
o pelean. Una plantilla mal aplicada no cuesta tokens, cuesta archivos que mienten.

Cuatro preguntas sobre el proyecto:

1. ¿Exportación estática, sin servidor?
2. ¿Bilingüe con rutas por idioma?
3. ¿Datos como JSON versionado en el repo?
4. ¿SEO como fuente de tráfico?

| "Sí" | Punto de partida |
|---|---|
| **3–4** | **Fase 1a** — clonar la plantilla tal cual. Es su arquetipo. |
| **1–2** | **Fase 1a** y borrar lo que no aplica. Más barato y más correcto que empezar de cero. |
| **0** | **Fase 1b** — scaffolder oficial del stack, y copiar de la plantilla solo lo que es agnóstico. |

El acoplamiento más profundo de la plantilla es la exportación estática: si el proyecto
necesita servidor (login, base de datos, middleware), arrancar de ahí es empezar peleando.

**Lo que se copia siempre, incluso en el caso "0":** la config estricta de TypeScript y
del linter, el módulo único de identidad del sitio, los helpers que omiten en vez de
inventar, el verificador de SEO atado a un solo comando, y el esqueleto de `AGENTS.md`
del proyecto. Son ~485 líneas que no dependen del arquetipo y que regenerar cuesta
más que copiar.

> **"Desde cero" nunca significa escribir el andamiaje a mano.** Significa scaffolder
> oficial + la lista de la Fase 1b + copiar lo agnóstico. Si un agente empieza a redactar
> el `package.json` o la config del compilador desde la nada, algo se hizo mal.

---

## Fase 1a — Sitio estático bilingüe: lo hace la plantilla

**No se arma el andamiaje a mano.** El template ya trae resuelto todo lo que antes eran
tres fases: TypeScript `strict`, estructura de carpetas, i18n con rutas traducidas,
sitemap, robots, canonicals, 404, redirección de la raíz, analytics por variable de
entorno y la verificación automática de SEO.

**El arranque es una sola instrucción al agente: "seguí SETUP.md".**

El paso 1 de ese archivo es el clone, y el agente lo corre solo. Son 7 pasos escritos en
el propio repo; no hay que explicárselos. Lo único que se agrega es el contexto que el
`SETUP.md` no puede saber: qué es el proyecto, su modelo de datos y sus reglas propias.

Conviene pedirle que **frene al terminar el paso 4**: del 1 al 3 es mecánico, pero las
rutas y el modelo de datos son decisiones, y moverlas después sale caro.

> **Clonar no es estar listo.** Faltan ~15 minutos: reemplazar el `AGENTS.md` heredado,
> el dominio, las rutas, el modelo de datos y los textos. El build **falla a propósito**
> si no está el dominio, para que no se pueda deployar con canonicals rotas.

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
- [ ] **`AGENTS.md`**: apunta al vault y documenta lo propio del repo. El esqueleto
      está en `templates/AGENTS.project.md` de la plantilla — sirve igual para un
      proyecto que no salga de ella.
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
