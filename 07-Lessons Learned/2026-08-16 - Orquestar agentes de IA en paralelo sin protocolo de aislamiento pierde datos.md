---
tags: [lessons-learned, ia, agentes, concurrencia, pipelines]
updated: 2026-08-16
status: active
---

# Orquestar agentes de IA en paralelo sin protocolo de aislamiento pierde datos

> Delegar la misma tarea a N instancias de un agente de IA corriendo a la vez no es
> "llamar a una función pura N veces" — hay estado compartido de por medio, y si nadie
> lo diseña a propósito, se pierde trabajo sin ningún error visible.

---

## Síntoma

Se delegó la auditoría de research de compatibilidad de hardware (CompatCheck) a 5
instancias paralelas de un agente de IA en CLI (opencode, modelos gratis vía API), cada
una auditando un lote de research cruda contra el catálogo real y escribiendo su
veredicto a un documento compartido. Al cerrar, **una de las 5 secciones había
desaparecido del documento final** sin ningún error — se detectó recién porque un script
de carga generado por esa instancia referenciaba una fuente sin auditoría documentada
que la respaldara. Por separado, los scripts de carga que generaron las distintas
instancias tenían IDs incrementales hardcodeados que colisionaban entre sí según el
orden en que se corrieran. En una ronda posterior, al reintentar con el problema anterior
ya corregido, las instancias empezaron a colgarse sin completar la tarea, a fallar en
silencio al escribir el archivo de salida, o a devolver error 429 (rate limit) sin que
la herramienta abortara — quedaban indefinidamente "vivas" sin avanzar.

## Impacto

Trabajo real perdido sin aviso — la sección de auditoría faltante no generó ningún error,
se descubrió por inspección manual de un detalle no relacionado. Reprocesar significa
volver a gastar cuota gratis del modelo (limitada y compartida entre todas las
instancias). En el peor caso, si el problema de IDs colisionados no se detecta antes de
cargar a la base de datos real, puede insertar datos corruptos o descartar en silencio
productos válidos que otro script ya había reclamado el mismo ID.

## Causa raíz

Dos causas independientes, que se combinaron:

1. **Race condition de read-modify-write sobre un archivo compartido.** Las 5 instancias
   leían el mismo documento, armaban su propia versión en memoria con su sección
   agregada, y escribían. La que escribió último pisó el archivo completo, borrando el
   append de cualquier otra que hubiera escrito antes en la ventana entre su lectura y
   su escritura. Ninguna instancia sabía que las otras existían ni que compartían el
   mismo archivo de salida.
2. **ID incremental hardcodeado en vez de calculado en runtime.** Cada instancia escribió
   su script de carga asumiendo un punto de partida fijo para el próximo ID, sin
   consultar el estado real de los datos al momento de ejecutar. Como varias asumieron
   el mismo punto de partida, los scripts chocaban (si corrían en cierto orden) o se
   pisaban en silencio (si corrían en el orden contrario, con la guarda de dedup más
   débil de un script descartando erróneamente un producto legítimo solo porque su ID
   ya estaba tomado por otra cosa).

## Por qué no lo vi antes

El patrón "delegar una tarea a un agente de IA" se pensó implícitamente como una
operación aislada — se arma el prompt, se lanza, se espera el resultado — sin diseñar
qué pasa cuando hay **otras instancias del mismo tipo corriendo en simultáneo contra el
mismo estado compartido** (el mismo archivo, el mismo catálogo de datos). Ya existía la
lección de chequear que el working tree esté limpio antes de escribir (ver [[Git]]), pero
esa cubre el caso de una sesión **accidental** corriendo en paralelo sin saberlo — acá el
paralelismo era **intencional**, orquestado a propósito para acelerar el trabajo, y ese
caso concreto no tenía protocolo: se asumió que "lanzarlas todas y ver qué pasa" alcanzaba,
porque cada prompt individual estaba bien escrito y no tenía nada de malo en aislamiento.

## Fix

- Cada instancia en paralelo escribe a un archivo de salida **propio y único**, nunca a
  uno compartido. Consolidar los N archivos en uno es un paso aparte, hecho después por
  un humano o por un único proceso downstream.
- Ningún script de carga con ID incremental lo hardcodea: se calcula siempre en runtime
  contra el estado real (`max(existente) + 1`), sin importar el orden de ejecución entre
  scripts generados por instancias distintas.
- Al pedirle a una instancia que genere un output grande, instruir escritura incremental
  (crear con la primera porción, appendear el resto) en vez de un solo bloque final — el
  output truncado a mitad de una escritura grande es un fallo real y documentado en
  herramientas de agentes de código en CLI, no una hipótesis.
- Ante rate limit de una API gratis compartida entre instancias: no asumir que la
  herramienta va a fallar rápido y visible. Lanzar las instancias escalonadas (o
  directamente secuenciales si el problema persiste), nunca como ráfaga simultánea.

## Cómo lo prevengo

Antes de orquestar cualquier trabajo en N instancias paralelas de un agente de IA contra
un estado compartido (archivo, base de datos, API con cuota), el protocolo tiene que
responder explícitamente, por adelantado:

- ¿A dónde escribe cada instancia? (nunca un recurso que otra pueda tocar a la vez)
- ¿Cómo se resuelve un identificador incremental si dos instancias corren juntas?
  (nunca hardcodeado)
- ¿Qué pasa si una instancia se cuelga o pega un error de cuota? (asumir que va a pasar,
  no tratarlo como excepción)

Ver [[Content Pipelines]] (sección "Orquestar varios agentes de IA en paralelo") y
[[Static Data as Database]] (regla de ID en runtime) para el detalle aplicado.

---

## Enlaces

[[Content Pipelines]] · [[Static Data as Database]] · [[Git]] · [[Error Handling]]
