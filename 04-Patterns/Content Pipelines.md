---
tags: [patterns, contenido, automatizacion, ia, scraping]
updated: 2026-08-16
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
- **Un fetch vacío no prueba que la cita esté mal.** Un fetcher simple (sin ejecutar
  JS) puede devolver solo el shell de una página cuyo contenido real se carga por
  JavaScript — el dato puede ser real y la URL correcta, y aun así no aparecer en el
  HTML crudo. Antes de descartar una fuente por "no contiene el dato", probar una
  búsqueda indexada sobre esa misma URL/tema como corroboración — a veces el motor
  de búsqueda sí tiene el contenido renderizado que el fetch directo no consigue
  leer.

---

## Orquestar varios agentes de IA en paralelo

Cuando la fuente del pipeline no es un cron sino **varias instancias de un agente de IA
corriendo a la vez** (para acelerar research o auditoría), aparecen fallos que no existen
con una sola instancia — y por defecto no hay protocolo para ninguno de estos, hay que
diseñarlo a propósito:

- **Nunca output compartido.** Cada instancia escribe a su propio archivo, nunca a uno
  que otra instancia pueda estar leyendo/escribiendo al mismo tiempo. Es una race
  condition de read-modify-write: la que escribe último pisa el archivo entero y borra
  el trabajo de las demás, sin ningún error visible. Consolidar los N archivos en uno es
  un paso aparte, hecho después por un humano o por un único proceso downstream — nunca
  por los N agentes escribiendo al mismo lugar. Ver
  [[2026-08-16 - Orquestar agentes de IA en paralelo sin protocolo de aislamiento pierde datos]].
- **Ningún ID incremental hardcodeado.** Si dos instancias en paralelo generan cada una
  un script que asume "el próximo ID es X", van a chocar o (peor) pisarse en silencio
  según el orden real de ejecución. Se calcula siempre en runtime contra el estado real
  de los datos (`max(existente) + 1`), sin importar el orden en que terminen las
  instancias. Ver [[Static Data as Database]].
- **Output grande de una sola instancia: escritura incremental, no un bloque final.**
  Pedir que cree el archivo con la primera porción y vaya appendeando el resto, en vez
  de acumular todo y escribirlo de una — mitiga el fallo silencioso/cuelgue que varios
  agentes de código en CLI tienen cuando un único output supera el límite de tokens de
  salida del modelo y la generación se trunca a mitad de una escritura.
- **Cuota agotada en un agente-CLI, no en una llamada de API directa, no siempre falla
  rápido y visible.** La distinción de "cuota vs. error" de más abajo asume que el 429
  se puede detectar y degradar — pero varias herramientas que ejecutan agentes de IA
  (no la llamada a la API en sí) se quedan colgadas indefinidamente ante un rate limit
  en vez de abortar. Lanzar instancias escalonadas, no en ráfaga simultánea, y tener un
  plan para matar una instancia colgada en vez de esperarla sin límite de tiempo.

---

## Auditar research generado por IA antes de cargarlo

Cuando el "contenido crudo" no es texto para reescribir sino datos técnicos que un
research de IA (Gemini Deep Search, ChatGPT "investiga a fondo", etc.) devuelve con
fuente citada, la verificación no termina en "¿la URL existe?" — hay que auditar
campo por campo contra la fuente real antes de cargar. Tres patrones que costó
aprender:

- **Un número que parece imposible puede ser real.** Datasheets de hardware de alto
  ancho de banda (DisplayPort 2.1/UHBR20, HDMI 2.1) listan combos de
  resolución/refresh que sí caben en el ancho de banda total en abstracto aunque
  ningún monitor real los alcance (ej. "4K@960Hz" en un adaptador de 80Gbps). No
  descartar un número raro como fabricación solo porque suena absurdo — buscarlo en
  2+ fuentes independientes. Si aparece igual en ambas, es real aunque sea inusual;
  si es un número aislado sin corroborar en ningún otro lado, ahí sí sospechar.
- **Verificar que el identificador (SKU, ID, código) exista, no solo que el dato
  "suene bien".** Una IA de research puede inventar un código de modelo plausible
  para un producto que sí es real bajo otro identificador — las specs pueden
  coincidir con el producto correcto y aun así el SKU citado ser ficticio. Buscar el
  identificador exacto en al menos una fuente independiente de la URL citada, no
  solo confiar en que el resto del dato cuadra.
- **La fabricación de citas no se limita a SKUs — también pasa con citas de
  comunidad (foros, Reddit) que respaldan un "caveat" de compatibilidad.** La IA
  cita una URL real que existe de verdad, con un hilo real, pero que habla de un
  producto **distinto** al que se está describiendo (mismo patrón que "SKU de otro
  producto", aplicado a testimonios en vez de a specs). Se detecta abriendo el
  título del hilo citado, no solo confirmando que la URL resuelve — si el título no
  menciona el producto en cuestión, la cita es sospechosa aunque la URL sea 100%
  real. En una tanda de 9 productos esto apareció en 4 (~44%), todos del mismo
  informe — cuando aparece una vez, vale la pena revisar el resto del mismo informe
  con más sospecha en vez de asumir que fue un caso aislado.
- **Caso más específico y más peligroso de lo anterior: si la marca tiene una línea
  real dedicada a ese caso de uso (ej. un dock "para Steam Deck" explícito en su
  catálogo), el marketing real de esa línea aparece mal atribuido en citas de SKUs
  hermanos no relacionados de la misma marca.** No es un error obviamente ajeno —
  suena perfectamente coherente con el tema, así que baja la guardia del auditor
  más que la mezcla genérica. En una tanda con este patrón, ~70% de las citas
  resultaron fabricadas o mal atribuidas. Detalle completo y fix en
  [[2026-08-05 - Cita real pero del producto equivocado en research de IA]].
- **Cuando el identificador falla Y la fuente oficial ya no existe (404/discontinuo)
  Y las únicas fuentes secundarias disponibles se contradicen en un dato central,
  descartar el ítem en vez de forzar una corrección compuesta.** Corregir un SKU
  fabricado es razonable cuando hay una fuente sólida detrás; corregir un SKU
  fabricado *y* arbitrar entre specs contradictorias *y* sin página oficial viva es
  acumular demasiada incertidumbre en un solo registro — mejor omitirlo que
  cargarlo con tres asunciones encadenadas.
- **Para productos con un trade-off físico documentado** (ej. un switch que reparte
  ancho de banda entre video y datos), retailers B2B especializados (no genéricos)
  a veces documentan cada posición/modo con más detalle que el datasheet PDF oficial
  del fabricante — que frecuentemente es una imagen escaneada no legible por
  herramientas de fetch. Vale la pena buscar ese tipo de fuente cuando el datasheet
  oficial falla.
- **El dedup mecánico previo a auditar (comparar nombres/SKUs contra lo ya cargado)
  no es suficiente por sí solo cuando se procesan varios informes a la vez.** Un
  producto ya cargado en una sesión anterior puede reaparecer en un informe nuevo
  con una redacción de nombre ligeramente distinta ("TS4" vs "Thunderbolt Station 4
  (TS4)") que el dedup por texto no detecta antes de auditar — la auditoría de specs
  no lo revela tampoco, porque ambas versiones son "correctas" (describen el mismo
  producto real). Se detecta recién comparando `slug`/id final contra el dataset
  completo *después* de cargar. En una tanda de ~100 productos repartida en varios
  scripts de carga, esto dejó pasar 4 duplicados reales. Repetir siempre un chequeo
  de duplicados de slugs/ids sobre el archivo de datos completo después de cargar,
  no solo antes de auditar — son dos pasadas distintas, no una.
- **Una instrucción anti-fabricación en el prompt de research no garantiza que la IA
  la respete de forma pareja entre corridas.** En una tanda de 5 informes generados
  con el mismo prompt reforzado ("verificá que la cita hable del producto exacto"),
  2 informes salieron limpios y 3 mantuvieron el mismo patrón de citas mal atribuidas
  que la instrucción buscaba evitar — el resultado depende de la corrida individual,
  no solo del prompt. No asumir que agregar la instrucción una vez resuelve el
  problema para siempre; seguir auditando cada tanda como si la instrucción no
  existiera.
- **Truco de auditoría que escala bien para citas de Reddit: leer el *slug* de la
  URL en vez de abrir la página.** Reddit codifica una versión abreviada del título
  del hilo directamente en la URL (ej. `.../comments/1abc23/nombre_del_modelo_aqui/`).
  Si el slug no menciona el producto que la cita dice respaldar, se puede descartar
  la cita sin gastar una llamada de fetch — mucho más rápido que abrir cada URL para
  confirmar relevancia cuando hay decenas de citas para chequear en una sola tanda.

---

## Enlaces

[[Programmatic SEO]] · [[Error Handling]] · [[Validation]] · [[Node]]
