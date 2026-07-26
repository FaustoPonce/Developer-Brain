---
tags: [meta, index]
updated: 2026-07-26
---

# Developer Brain

Mi segundo cerebro como desarrollador. Todo lo que sé, decido y repito —
escrito una sola vez, en un solo lugar.

**Objetivo:** que cualquier agente de IA (o yo dentro de 6 meses) pueda leer
este vault y trabajar exactamente como trabajo yo, sin tener que explicarlo de nuevo.

> **¿Sos un agente?** No leas esto: leé `CLAUDE.md`. Tiene las reglas que aplican siempre
> y una tabla que te dice qué notas abrir según la tarea. Este README es el índice para
> humanos; leer el vault entero cuesta ~30k tokens y casi nunca hace falta.

---

## Cómo se usa

1. **Antes de arrancar un proyecto** → [[New Project]]
2. **Antes de decidir algo grande** → [[Decision Making]]
3. **Cuando algo se rompe** → [[Debugging]]
4. **Cuando algo se rompe y aprendí algo** → nota nueva en `07-Lessons Learned/`
5. **Cuando repito un prompt 2 veces** → nota nueva en `08-Prompts/`
6. **Antes de deployar** → checklist en `09-Checklists/`

Regla: si algo lo expliqué dos veces, va al vault.

---

## Reglas del vault

Las cuatro reglas que deciden qué se escribe acá. Ante la duda, se aplican en este orden.

### 1. Principios, no implementación

**No guardo código.** Guardo la regla que el código obedece.

| No entra | Entra |
|---|---|
| `export async function getUser...` | "Para acceder a datos, siempre pasar por un Repository" |
| Un controller completo | "Nunca lógica de negocio dentro del controller" |
| Un componente con su fetch | "Nunca mezclar fetch con componentes de UI" |

El código envejece en semanas. El principio dura años.

**Las únicas dos excepciones**, y ambas van etiquetadas:

- **Contraste ❌/✅** de 6 líneas o menos, cuando el principio se entiende más rápido
  viéndolo que leyéndolo. Nunca una función completa: sin imports, sin nombres de
  archivos, sin implementación.
- **Plantillas y contratos** marcados con `> Plantilla.` o `> Contrato.` — config que
  copio tal cual (`tsconfig`, `Dockerfile`) o formas que todas mis APIs respetan.
  No son ejemplos de cómo escribir código; son la cosa en sí.

Regla práctica: si el bloque tiene nombres inventados de variables, servicios o archivos
(`gameRepo`, `orderService`, `GameCard`), es implementación y no va.

### 2. El filtro de los 6 meses

> **¿Esto me va a servir dentro de 6 meses? → entra.**
> **¿Solo sirve para el proyecto de hoy? → no entra.**

Es el único filtro que necesito. Si dudo, no entra: el vault se muere por exceso de ruido, no por falta de notas.

### 3. Lo que NO entra

- Bugs específicos de un proyecto
- Nombres de clientes o de proyectos
- APIs temporales, endpoints, URLs
- Nombres de variables, funciones o archivos concretos
- Versiones exactas de dependencias

Todo eso envejece rápido y ensucia. Va en el `README.md` o el `CLAUDE.md` **del repo**, no acá.

**Excepción:** en `07-Lessons Learned/` el caso concreto sí se anota, pero solo como evidencia.
El valor de la nota es el principio que extraigo, no la crónica del bug.

### 4. El archivo que manda

[[Development Philosophy]] es la nota raíz. Todo lo demás se deriva de ella y no puede contradecirla.
Si una nota choca con la filosofía, o la nota está mal, o la filosofía cambió y hay que actualizarla.

---

## Estructura

| Carpeta | Qué vive acá |
|---|---|
| `01-Identity` | Cómo pienso. Lo más estable del vault. |
| `02-Tech Stack` | Mi stack: por qué lo elegí y cómo lo uso. |
| `03-Architecture` | Cómo estructuro un sistema. |
| `04-Patterns` | Patrones concretos, a nivel código. |
| `05-Workflow` | Procesos: del `git init` al deploy. |
| `06-Skills` | Skills reutilizables para agentes de IA. |
| `07-Lessons Learned` | Post-mortems. Errores que no quiero repetir. |
| `08-Prompts` | Prompts probados que funcionan. |
| `09-Checklists` | Listas mecánicas. Sin pensar, solo tildar. |

---

## Convenciones de las notas

- **Frontmatter** en todas: `tags`, `updated`, y `status` donde aplique.
- **Enlaces** con `[[wikilinks]]` — el grafo es la mitad del valor.
- **Español** para explicaciones, **inglés** para nombres de archivos, código y términos técnicos.
- Cada nota responde: *qué es*, *por qué lo hago así*, *cómo se ve en código*.
- Nada de teoría copiada de la docs oficial. Solo lo que **yo** decidí.

### Estados

- `draft` — esbozo, todavía no lo apliqué.
- `active` — lo uso hoy, es la fuente de verdad.
- `deprecated` — ya no lo hago así, pero dejo el registro y el por qué.

---

## Puntos de entrada

- [[Development Philosophy]] — el punto de partida de todo.
- [[Coding Principles]] — las reglas duras.
- [[Folder Structure]] — cómo se ve un repo mío por dentro.
- [[New Project]] — de cero a deployado.
