---
tags: [patterns, contenido, automatizacion, ia, scraping]
updated: 2026-07-26
status: active
---

# Content Pipelines

> Cómo mantengo un sitio estático con contenido fresco sin backend ni base de datos.

---

## La arquitectura

**El contenido es un archivo de datos versionado en el repo.** Un trabajo programado
lo actualiza, lo commitea, y el commit dispara el rebuild.

```
cron → obtener fuente → normalizar → enriquecer → escribir dato → commit → rebuild
```

Por qué me sirve: cero infraestructura, cero costo, el historial de git es el historial
del contenido, y puedo revertir una corrida mala con un `revert`.

El límite: no sirve para datos que cambian por minuto ni para contenido por usuario.
Para todo lo demás —catálogos, noticias, precios diarios— alcanza y sobra.

---

## Reglas del pipeline

1. **Idempotente.** Correrlo dos veces seguidas no duplica nada. La deduplicación va
   por clave estable (identificador de origen y slug), no por posición ni por índice.
2. **Incremental y reanudable.** Cada corrida procesa solo lo que falta. Si se corta
   a la mitad, la siguiente sigue donde quedó.
3. **Acotado por corrida.** Cuando hay una cuota externa, se procesa un lote fijo y el
   resto queda para mañana. El pipeline **converge en varias corridas** en vez de fallar
   en una.
4. **Solo commitea si hay cambios.** Si no cambió nada, no hay commit y no hay rebuild.
5. **Nunca borra por antigüedad.** El tope es por cantidad, no por fecha.
   Ver [[2026-07-26 - Borrar contenido viejo genero 404 permanentes]].
6. **Los efectos secundarios best-effort no rompen la corrida.** Avisarle a los
   buscadores que hay URLs nuevas es deseable, no crítico: si falla, se loguea y sigue.
7. **Falla ruidosa en lo crítico, silenciosa en lo opcional.** La distinción se decide
   de antemano, no en el `catch`.

---

## Distinguir cuota de error

El error más útil que aprendí acá: **quedarse sin cuota no es lo mismo que fallar.**

- Cuota agotada (429, límite de tasa) ⇒ degradar: pasar al siguiente proveedor o modelo,
  o dejarlo para la próxima corrida.
- Cualquier otro error ⇒ abortar y que se vea. Es un fallo real que quiero saber.

Tratar todo como error hace que el pipeline se caiga por algo previsible.
Tratar todo como cuota esconde bugs reales. Ver [[Error Handling]].

---

## Generar contenido con un LLM

Lo uso para convertir un dato crudo en un artículo legible. Lo que hace que funcione:

- **Grounding obligatorio.** Primero se busca el texto real de la fuente y se le pasa
  al modelo como material. Sin fuente, el modelo escribe algo general y **se marca como
  no fundamentado**; nunca se le pide que rellene con detalles.
- **Prohibición explícita de inventar** datos concretos: fechas, cifras, nombres, precios.
  Ante la duda, que quede general. Ver [[2026-07-26 - Datos fabricados en schema y metadatos]].
- **Salida estructurada y validada.** Se pide un formato exacto y se **valida la forma**
  antes de usarla. La respuesta de un modelo es input externo como cualquier otro:
  se parsea, se valida, y si no cumple se descarta. Ver [[Validation]].
- **Reescritura, no copia.** La instrucción de reformular con palabras propias es parte
  del contrato, no un detalle de estilo: copiar la fuente es contenido duplicado.
- **Referencias de modelo estables.** Apuntar a alias que sobreviven a que el proveedor
  retire versiones puntuales.
- **Cadena de degradación por costo:** primero el modelo bueno, después el barato.

---

## Scraping

- User-agent real y timeout explícito en toda petición.
- El parseo de la fuente **falla hacia afuera**: si no se puede extraer un campo, se
  omite. Nunca se completa con un valor plausible.
- El HTML ajeno cambia sin avisar: asumir que el selector se va a romper y que eso
  tiene que ser visible, no silencioso.
- Guardar la fuente cruda cuando el volumen lo permite: debuggear sin poder reproducir
  la entrada es imposible.

---

## Enlaces

[[Programmatic SEO]] · [[Error Handling]] · [[Validation]] · [[Node]]
