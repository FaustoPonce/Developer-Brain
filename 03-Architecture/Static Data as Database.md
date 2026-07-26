---
tags: [architecture, datos, estatico, seo]
updated: 2026-07-26
status: active
---

# Static Data as Database

> Un archivo JSON versionado en git como única fuente de datos, sin base de datos.
> Es la decisión que sostiene todos mis sitios de contenido, y nunca la había escrito.

---

## Cuándo sirve

- Los datos los **edito yo o un script**, no los usuarios.
- Se actualizan por día o por semana, no por minuto.
- El sitio es de lectura: nadie escribe desde el navegador.
- No hay contenido por usuario ni sesiones.

Eso cubre catálogos, directorios, comparadores, noticias y páginas generadas a escala.
O sea: casi todo lo que hago.

## Cuándo no

- Datos que cambian por minuto, o precios en vivo.
- Escrituras desde el cliente.
- Contenido por usuario.
- Cuando la consulta necesita filtros combinados sobre miles de registros **en runtime**
  (en build no importa: filtrar en memoria es gratis).

---

## Lo que gano

| | |
|---|---|
| **Costo** | Cero. Sin base de datos que pagar ni mantener. |
| **Velocidad** | Todo se resuelve en build. El sitio servido es HTML plano. |
| **Historial** | El log de git **es** el historial del contenido. Puedo ver qué cambió y cuándo. |
| **Rollback** | Un `revert` deshace una actualización de datos mala. |
| **Sin infra** | No hay pool de conexiones, ni backups, ni migraciones. |
| **Diffs revisables** | Un cambio de datos se revisa como se revisa código. |

Ese cuarto punto es el que más veces me salvó: un script que corrompe datos se
deshace con un comando, no con una restauración de backup.

---

## Reglas

1. **La app nunca importa el JSON directo.** Siempre a través de una capa de acceso.
   Es lo que permite normalizar en un solo lugar y cambiar el origen después sin tocar
   las páginas. Ver [[Repositories]].
2. **Normalizar en el borde.** Los datos reales llegan sucios: el mismo campo con
   booleanos y strings mezclados, fechas en formatos distintos, títulos con problemas de
   codificación. Se normaliza **una vez**, al cargar, y adentro el tipo es confiable.
3. **Descartar, no rellenar.** Un registro sin los campos mínimos se descarta con un
   aviso. No se completa con valores plausibles.
   Ver [[2026-07-26 - Datos fabricados en schema y metadatos]].
4. **Una puerta de calidad separada del acceso.** "Qué datos existen" y "qué datos se
   publican" son dos preguntas distintas. Ver [[Programmatic SEO]].
5. **El script que actualiza es idempotente** y no borra por antigüedad.
   Ver [[Content Pipelines]].
6. **JSON, no YAML ni CSV.** Parseo nativo, sin dependencias, y diffs legibles.

---

## El límite real: tamaño

Acá está el trade-off que hay que vigilar, y tiene un número.

Con exportación estática, **el archivo importado entra al bundle de las páginas que lo
usan**. Un catálogo de ~1,6 MB con ~700 entradas todavía funciona, pero:

- El build se hace notoriamente más lento.
- Si el import cae en un componente cliente, se descarga **entero** en el navegador.
- El diff de git deja de ser revisable a ojo.

**Señales de que hay que cambiar de estrategia:**

| Señal | Qué hacer |
|---|---|
| El archivo pasa ~2 MB | Partirlo por dominio (uno por categoría o por idioma) |
| El build tarda de más | Cargar solo lo necesario por página, no el catálogo completo |
| El JSON aparece en el bundle cliente | Mover el acceso a Server Components |
| Los datos los editaría otra persona | CMS headless, no JSON |
| Hace falta escribir desde el navegador | Base de datos de verdad. Ver [[PostgreSQL]] |

La regla práctica: **mientras el diff de una actualización se pueda revisar leyéndolo,
el JSON alcanza.** Cuando deja de poder revisarse, ya es una base de datos mal hecha.

---

## Backup

No hace falta uno: el repo remoto **es** el backup, con historial completo.
Es la única categoría de proyecto donde puedo decir eso honestamente.

---

## Enlaces

[[Programmatic SEO]] · [[Content Pipelines]] · [[Repositories]] · [[NextJS]] · [[Decision Making]]
