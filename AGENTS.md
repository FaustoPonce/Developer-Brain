# Developer Brain — instrucciones para agentes

Este repo es el cerebro de desarrollo de Fausto. Contiene cómo trabaja, qué decidió y
qué errores ya pagó, para no re-explicarlo en cada sesión.

**Leé este archivo completo. Después leé SOLO las notas que la tabla de ruteo indique
para la tarea que tenés entre manos.** No leas el vault entero: son 57 notas. El objetivo de este archivo es que no tengas que hacerlo.

---

## Cómo trabajar

- **Responder en español**, directo, sin preámbulos ni resúmenes largos.
- Proponer **una** solución recomendada, no un menú de opciones.
- Si algo que pidió está mal, decirlo en una o dos frases y hacerlo igual.
- Seguir las convenciones que ya están en el repo, aunque no sean las preferidas.
- No agregar abstracciones, capas ni dependencias "por buenas prácticas".
- No inventar datos, precios ni contenido. Si no se sabe, se dice.
- No decir "listo" sin haberlo verificado.

---

## Hechos que aplican siempre

Esto evita los errores que ya costaron tráfico y plata. **Si la tarea toca alguno de
estos temas, estas reglas ganan sin necesidad de leer más.**

**Stack por defecto:** Next.js App Router + TypeScript `strict` + Tailwind, exportación
estática, datos en JSON versionado en el repo, deploy en Cloudflare Pages o Vercel,
bilingüe EN/ES. Tráfico = SEO orgánico. Monetización = AdSense + afiliados.

**SEO — no negociable:**
- Canonical **absoluta**, y derivada de la identidad del contenido, **nunca** del
  parámetro de la URL.
- Toda ruta **declara explícitamente en qué idiomas existe** al generar parámetros
  estáticos. Path traducido (`/privacy` vs `/privacidad`) ⇒ dos carpetas, cada una emite
  solo su idioma. Path idéntico en ambos (`/faq`) ⇒ una carpeta que emite los dos.
  Heredar el idioma del segmento padre genera duplicados por producto cartesiano.
- `lastmod` refleja cambios de contenido reales. **Nunca la fecha del build.**
- Al sitemap entra solo lo que tiene contenido propio, no todo lo que existe.
- Nunca borrar contenido por antigüedad: una URL indexada es un compromiso permanente.
- El defecto técnico que **bloquea la indexación va primero**, siempre. Sin índice no
  hay ranking, sin ranking no hay cita de IA, y ninguna mejora de contenido se mide.
- **Google ignora `llms.txt`** (posición explícita). No es palanca de ranking ni de
  citación; mantenerlo es opcional y de bajo retorno. Lo que sí mueve citas de IA:
  frescura, respuesta autocontenida arriba de la página, menciones de marca.
- Traducir un catálogo **no lo convierte en contenido nuevo**: Google nombra las
  transformaciones automáticas dentro de *scaled content abuse*.
- **Al auditar Search Console:** impresiones, CTR y posición no son confiables entre
  2025-05-13 y 2026-04-27 (bug de logging de GSC, sin backfill; clics no afectados).
  Cualquier tendencia que cruce ese rango se lee con cautela. Detalle en
  `06-Skills/imported/seo-google`.
- **"Página con redirección" que sube o no valida no es necesariamente un bug.** Si
  el motivo es un redirect permanente por diseño (protocolo, trailing slash, www,
  raíz de idioma), el botón "Validar corrección" nunca va a pasar — la condición
  que chequea es verdadera para siempre. Verificar con `curl -L` sobre los
  ejemplos reales antes de sospechar una regresión. Ver
  [[2026-08-21 - Pagina con redireccion en Search Console nunca valida si el redirect es permanente]].

**Monetización:**
- No reaplicar a AdSense hasta que Search Console muestre las páginas corregidas ya
  reindexadas. Reaplicar antes hace que la revisión evalúe el estado viejo, y un
  rechazo consecutivo endurece las siguientes.

**Datos:**
- Sin dato real, el campo no se emite. **Prohibidos** los valores por defecto plausibles
  (`?? 60`, precio fijo, fecha de hoy al fallar un parseo) en metadatos, datos
  estructurados o texto visible. Un hueco es honesto; un número inventado es un bug.
- Validar todo lo que entra de afuera, incluida la respuesta de una API y la salida
  de un LLM.

**Código:**
- Cero `any`. Sin `catch` vacíos. Sin números mágicos.
- No abstraer hasta el tercer caso repetido.
- Agrupar por feature, no por tipo de archivo.

---

## Tabla de ruteo

| Si la tarea es… | Leer |
|---|---|
| **Proyecto nuevo: sitio estático bilingüe con SEO** | **No armar andamiaje a mano.** Clonar `Template proyectos/Pages-Template` y seguir su `SETUP.md`. Después `05-Workflow/New Project.md` (Fase 1a). |
| **Proyecto nuevo: cualquier otro tipo** | `05-Workflow/New Project.md` → Fase 1b tiene la lista de setup independiente del stack. |
| Sitio bilingüe / rutas por idioma / hreflang | `03-Architecture/Internationalization.md` |
| Generar muchas páginas desde datos | `04-Patterns/Programmatic SEO.md` |
| Aparecer citado en AI Overviews / ChatGPT / Perplexity | `04-Patterns/Generative Engine Optimization.md` |
| Calidad de contenido, autoría, YMYL, "¿este nicho es para mí?" | `04-Patterns/E-E-A-T.md` |
| Scraping, pipeline de contenido, generar texto con IA | `04-Patterns/Content Pipelines.md` + el prompt probado en `08-Prompts/` |
| Datos del sitio: JSON, catálogo, sin base de datos | `03-Architecture/Static Data as Database.md` |
| Antes de publicar o tras cambiar rutas | `09-Checklists/SEO Launch.md` |
| Caída de tráfico, Search Console, duplicados | `07-Lessons Learned/` (20 notas) |
| Cualquier cosa de Next.js | `02-Tech Stack/NextJS.md` |
| Componentes, UI, React | `04-Patterns/Components.md`, `04-Patterns/Hooks.md` |
| Estado en el frontend | `03-Architecture/State Management.md` |
| API, backend, endpoints | `03-Architecture/API Design.md`, `04-Patterns/Services.md` |
| Acceso a datos, queries | `04-Patterns/Repositories.md` |
| Validación, formularios, env vars | `04-Patterns/Validation.md` |
| Login, permisos, sesiones | `03-Architecture/Authentication.md` |
| Manejo de errores, logging | `03-Architecture/Error Handling.md` |
| Cómo nombrar algo | `04-Patterns/Naming.md` |
| Debuggear | `05-Workflow/Debugging.md` |
| Deployar | `05-Workflow/Deployment.md` |
| Commits, branches | `05-Workflow/Git.md` |
| Revisar código | `05-Workflow/Code Review.md`, `01-Identity/Coding Principles.md` |
| Decisión técnica grande o irreversible | `01-Identity/Decision Making.md` |
| Entender el criterio de fondo | `01-Identity/Development Philosophy.md` |
| TypeScript, tipos | `02-Tech Stack/TypeScript.md` |
| Postgres, Docker, Node, ASP.NET | la nota correspondiente en `02-Tech Stack/` |

Si la tarea no encaja en ninguna fila, leer `00-README.md` y decidir desde ahí.

---

## No leer

- **`06-Skills/imported/`** — 50 skills copiadas de la comunidad, **sin verificar** y de
  calidad despareja. Solo abrir una si la tarea es específicamente sobre ella.
- **`06-Skills/catalog/`** — índice de 2049 skills externas. Son ~400 KB de tablas.
  Usar solo para buscar si existe una skill para algo puntual, nunca leer completo.

---

## Si vas a escribir en este vault

Cuatro reglas, en orden. Están completas en `00-README.md`.

1. **Principios, no implementación.** No se guarda código. Excepciones: contraste ❌/✅
   de ≤6 líneas, y plantillas marcadas con `> Plantilla.` o `> Contrato.`
   Si el bloque tiene nombres inventados de variables o servicios, es implementación y no va.
2. **Filtro de los 6 meses.** ¿Sirve en 6 meses? Entra. ¿Solo para el proyecto de hoy?
   No entra.
3. **No entran** bugs de un proyecto, nombres de clientes o dominios, APIs temporales,
   nombres de archivos concretos ni versiones de dependencias. Eso va en el repo del proyecto.
   Excepción: en `07-Lessons Learned/` el caso concreto entra como evidencia, pero el
   valor de la nota es el principio.
4. **`01-Identity/Development Philosophy.md` manda.** Nada puede contradecirla.

Formato: frontmatter con `tags`, `updated`, `status` (`draft` | `active` | `deprecated`).
Enlaces con `[[wikilinks]]`. Español para explicar, inglés para nombres de archivo y
términos técnicos.

Lecciones: `YYYY-MM-DD - descripción corta.md`. Toda lección tiene que **cambiar algo**
—una nota, una checklist o un test— o se va a repetir.

---

## Cómo usar esto desde otro repo

Este archivo solo ahorra cuota si los proyectos apuntan acá. En el `AGENTS.md` de cada
proyecto va una línea:

> Convenciones y decisiones: leer `AGENTS.md` de `OBJETIVOS/Developer-Brain/Developer-Brain/`
> y seguir su tabla de ruteo. Lo de acá abajo son solo los datos propios de este repo.

El `AGENTS.md` del proyecto se queda con lo que **no** puede vivir en el vault: dominio,
IDs de analytics y AdSense, comandos de build y deploy, estructura de su archivo de datos.
