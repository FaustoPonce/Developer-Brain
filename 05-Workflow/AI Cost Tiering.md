---
tags: [workflow, ia, costos]
updated: 2026-08-02
status: draft
---

# AI Cost Tiering

> No toda tarea que usa un LLM necesita el modelo de razonamiento fuerte.

---

## El problema

Delegar research masivo o extracción de datos a un agente de razonamiento (Claude
con subagentes, por ejemplo) es caro para lo que rinde: cada corrida carga contexto
propio, hace varias rondas y devuelve un reporte largo cuando lo que se necesita son
unos pocos campos estructurados. Multiplicado por decenas de entidades, se quema
cuota rápido sin que la tarea en sí sea difícil.

## El criterio

Separar la tarea por tipo de esfuerzo, no tratarla como un bloque:

- **Research con fuente obligatoria** (buscar un dato real y su URL) → un modelo con
  grounding nativo (ej. Gemini con Google Search grounding, sobre todo en modo "Deep
  Search"/investigación profunda) reduce cuánto tenés que buscar vos, pero **no
  elimina la necesidad de auditar** — ver corrección más abajo.
- **Transformación mecánica** (texto semi-estructurado → JSON contra un schema) → un
  modelo rápido/barato (ej. gpt-oss-120b vía Groq). Es formato, no razonamiento. Ojo
  si se corre dentro de un agente de código (Kilo, Cline, etc.): el overhead fijo de
  ese wrapper (definiciones de herramientas, contexto del entorno) puede superar el
  límite de tokens/minuto de un tier gratuito antes de sumar una palabra de la tarea
  real — para tareas de puro texto, usar el modelo directo (playground/API), no el
  agente de código.
- **Validación, auditoría de una muestra, y escritura en el código real** → el modelo
  de razonamiento fuerte, al final, con el volumen de trabajo ya reducido.

Ningún modelo barato es automáticamente más obediente a reglas de "no inventar" —
la disciplina de sourcing (ver [[Content Pipelines]] § Scraping) se le exige igual, y
se audita una muestra del resultado antes de confiar en el pipeline completo.

**Corrección (2026-08-02):** "grounding nativo" no da citas confiables por sí solo.
Probado con Gemini pidiendo specs + URL de ~18 productos: en modo normal, dos
corridas independientes del mismo prompt (una por producto, no en lote) devolvieron
URLs muertas o armadas por patrón — mismo producto, distinto SKU y URL en cada
corrida, señal de que completaba por patrón en vez de buscar de verdad. El modo Deep
Search mejoró bastante (bien: varios productos devueltos vacíos en lugar de
inventados, señal de que sí respeta "omitir si no encontrás"), pero al auditar con
fetch real seguían apareciendo campos de spec agregados sin base en la página
citada, y un caso de fuente citando el producto equivocado (dos SKUs del mismo
fabricante con nombre casi idéntico). **La causa raíz es estructural, no depende de
qué producto de IA se use**: "buscar" y "citar" resueltos por el mismo modelo en la
misma pasada generativa no tienen forma de auto-verificarse. Lo confiable es separar
los pasos — una búsqueda real (API/tool de search, no la memoria del modelo) que
devuelve URLs que existen por construcción, y después extracción sobre el contenido
ya traído de esa URL, donde el modelo no puede inventar la fuente porque ya se la
diste vos. El grounding de un chat ahorra el paso de "encontrar candidatos", nunca
el de auditar.

## Cuándo no vale la pena

Si la tarea es chica (unas pocas entidades) o requiere juicio (decidir si un dato es
suficientemente confiable, resolver ambigüedad de SKU), el costo de coordinar tres
herramientas supera el ahorro. El tiering se justifica por volumen.

---

## Enlaces

[[Content Pipelines]] · [[Programmatic SEO]]
