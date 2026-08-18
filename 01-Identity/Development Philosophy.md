---
tags: [identity, philosophy]
updated: 2026-07-26
status: draft
---

# Development Philosophy

> Envío productos, no código. El código es el medio.

---

## Contexto: quién soy como dev

- Solo dev. No hay equipo que absorba mi complejidad — cada abstracción de más la pago yo.
- Trabajo en varios proyectos en paralelo. Si vuelvo a un repo en 3 meses, tiene que ser obvio.
- Monetizo con AdSense / suscripciones. Un sitio que no indexa o no carga, no existe.
- Uso IA como par de programación. Escribo pensando en que otro agente lo va a leer.

---

## Principios rectores

### 1. Enviar > perfeccionar
Un feature en producción con deuda técnica conocida vale más que uno perfecto sin deployar.
Lo que no está deployado no genera nada.

### 2. Lo aburrido gana
Elijo la tecnología aburrida y probada. La novedad se paga en horas de debug a las 2 AM.
Ver [[NextJS]] y [[PostgreSQL]] — cada elección tiene un "por qué" escrito.

### 3. Optimizar para el yo del futuro
El lector principal del código soy yo en 6 meses, sin contexto.
Nombres explícitos > comentarios. Estructura predecible > estructura inteligente.

### 4. Complejidad solo cuando duele
No abstraigo hasta el tercer caso repetido. La abstracción prematura es más cara
que la duplicación.

### 5. Medir antes de opinar
Performance, SEO, conversión: número o no pasó. Lighthouse, Search Console, analytics.

### 6. Automatizar lo que hice 3 veces
La tercera vez que ejecuto algo a mano, se convierte en script, skill o checklist.

---

## Lo que NO hago

- Microservicios en un proyecto de un solo dev.
- Reescribir algo que funciona porque salió un framework nuevo.
- Optimizar performance sin haber medido.
- Tests por cobertura. Tests por riesgo. Ver [[Coding Principles]].
- Custom cuando la librería estándar alcanza.

---

## Cómo quiero que trabaje un LLM conmigo

Esta sección es para los agentes que leen este vault.

**Sí:**
- Español, directo, sin fluff ni preámbulos.
- Proponer **una** solución recomendada, no un menú de opciones.
- Decirme cuando algo que pedí está mal, en una o dos frases, y después hacerlo igual.
- Terminar la tarea completa. Si algo queda afuera, decirlo explícitamente.
- Seguir las convenciones que ya están en el repo, aunque no sean las que la IA prefiere.
- Preguntar solo cuando dos lecturas de mi pedido llevan a trabajos distintos.

**No:**
- Reescribir cosas que no pedí que toque.
- Agregar abstracciones, capas o dependencias "por buenas prácticas".
- Inventar datos, precios o contenido. Si no lo sabe, lo dice.
- Decir "listo" sin haberlo verificado.
- Resúmenes largos de lo que acaba de hacer.

**Contexto que doy siempre:** stack, si es proyecto nuevo o existente, y qué archivo tocar.
**Contexto que espera encontrar:** este vault + el `AGENTS.md` del repo.

---

## Cómo se traduce esto

| Filosofía | Se ve en |
|---|---|
| Enviar > perfeccionar | [[Deployment]], [[New Project]] |
| Lo aburrido gana | [[NextJS]], [[PostgreSQL]] |
| Yo del futuro | [[Naming]], [[Folder Structure]] |
| Complejidad tardía | [[Clean Architecture]] |
| Automatizar | `06-Skills/`, `09-Checklists/` |

---

## Enlaces

- [[Coding Principles]]
- [[Decision Making]]
