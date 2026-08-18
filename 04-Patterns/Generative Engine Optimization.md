---
tags: [patterns, seo, geo, ia, citaciones]
updated: 2026-08-12
status: active
---

# Generative Engine Optimization

> Aparecer citado en AI Overviews, AI Mode, ChatGPT y Perplexity. En 2026 menos de un
> tercio de las búsquedas de Google terminan en un clic, así que la cita ya no es un
> extra: para muchas consultas es todo el resultado.

---

## La posición de Google, que evita la mitad del trabajo inútil

**Optimizar para IA generativa sigue siendo SEO.** Es la posición oficial de Google:
"AEO" y "GEO" son etiquetas nuevas para el mismo trabajo. Cuando una recomendación de
la comunidad contradice la fuente primaria de Google, gana Google.

De ahí sale lo que Google **rechaza explícitamente** como palanca: `llms.txt`,
partir el contenido en *chunks* para la IA, reescribir texto "para que lo entienda un
modelo", y sembrar menciones artificiales. Ver [[AI Crawler Discoverability]].

**No existe un opt-out específico de IA.** La aparición en AI Overviews y AI Mode se
gobierna con las directivas de vista previa que ya existen (`nosnippet`,
`data-nosnippet`, `max-snippet`, `noindex`) — que son cosa distinta de los controles
de `robots.txt` para crawlers de IA de terceros.

---

## El piso de elegibilidad

**Si la página no está indexada, nada de esto importa.** No hay cita sin índice.

Es la misma jerarquía que aplica a todo: el defecto técnico que bloquea la indexación
va primero, siempre, aunque sea menos interesante que las tareas de crecimiento.
Ver [[Decision Making]].

---

## Menciones de marca > backlinks

El hallazgo que más cambia dónde poner el esfuerzo (estudio de Ahrefs sobre 75.000
marcas, dic-2025): **las menciones de marca correlacionan ~3x más con visibilidad en
IA que los backlinks.**

| Señal | Correlación con citas de IA |
|---|---|
| Menciones en YouTube | ~0,74 (la más fuerte) |
| Menciones en Reddit | alta |
| Presencia en Wikipedia | alta |
| Domain Rating (backlinks) | ~0,27 (débil) |

Consecuencia práctica: el presupuesto que iría a construir links rinde más en
presencia real en comunidades y video.

---

## Son varios motores, no uno

**AI Overviews y AI Mode llegan a la misma conclusión ~86% de las veces pero citan la
misma URL solo ~14%.** Y solo ~11% de los dominios son citados por ChatGPT y por
Google AIO para la misma consulta.

| Superficie | De dónde saca las citas |
|---|---|
| **Google AI Overviews** | Fuertemente correlacionado con ranking clásico: cita lo que ya rankea |
| **Google AI Mode** | Débilmente correlacionado, pool más amplio. Pesan frescura y autoridad de entidad más que la posición |
| **ChatGPT** | Wikipedia (~48%), Reddit (~11%) |
| **Perplexity** | Reddit (~47%), Wikipedia |

Que la experiencia de usuario se haya unificado en un solo flujo no cambia esto: por
debajo siguen siendo motores de cita distintos. Rankear bien alimenta AI Overviews,
pero no alcanza para AI Mode.

---

## Qué hace citable a un pasaje

- **Bloques autocontenidos de ~135-165 palabras.** Tienen que poder extraerse y
  seguir teniendo sentido sin el contexto de la página.
- **Adelante.** ~44% de las citas salen del primer 30% de la página. La respuesta va
  arriba, no al final del razonamiento.
- **Respuesta directa en las primeras 40-60 palabras de la sección.**
- **Definición con patrón explícito**: "X es…", "X se refiere a…".
- **Dato específico y atribuido.** Una cifra con fuente es citable; una afirmación
  general no.
- **Encabezados en forma de pregunta**, jerarquía limpia, párrafos de 2-4 oraciones,
  tablas para lo comparativo.
- **Dato propio que no existe en otro lado.** Es la única ventaja que no se copia.

Lo que no funciona: afirmaciones vagas, opinión sin evidencia, conclusiones enterradas.

---

## Frescura: la palanca más subestimada

Contenido de menos de 3 meses tiene ~3x más probabilidad de ser citado, y una página
sin tocar 6+ meses **pierde elegibilidad de cita** (estudio de SE Ranking sobre 1,3M
de citas).

Eso convierte un programa de refresco agendado en una de las acciones de mayor
rendimiento — y choca de frente con la tentación de dar un catálogo por terminado.

Ojo con la trampa conocida: refrescar significa **cambiar el contenido**, no tocar la
fecha. Ver [[2026-07-26 - lastmod dinamico quema la confianza de Google]].

---

## Los crawlers de IA no ejecutan JavaScript

El renderizado en servidor es condición, no optimización. **Un sitio de exportación
estática ya cumple esto por construcción** — es una ventaja gratis de la arquitectura
por defecto. Ver [[Static Data as Database]].

Los *fetchers disparados por el usuario* (navegación agéntica, herramientas que
buscan una URL puntual) **ignoran `robots.txt` por diseño**. No se bloquean desde ahí;
si hace falta, se bloquean del lado del servidor.

---

## Herramientas interactivas

Una herramienta o calculadora real es lo único que un resumen de IA no puede
reemplazar: puede contestar un dato suelto, no correr un filtro. En un sitio de datos,
esa es la parte defendible del tráfico.

---

## Enlaces

[[Programmatic SEO]] · [[AI Crawler Discoverability]] · [[E-E-A-T]] · [[SEO Launch]] ·
[[Content Pipelines]]
