---
tags: [architecture, i18n, seo, routing]
updated: 2026-07-26
status: active
---

# Internationalization

> Todos mis sitios son bilingües (EN/ES). El SEO multiidioma es donde más URLs basura
> generé, así que las reglas de acá son caras.

---

## Decisiones fijas

| Tema | Decisión |
|---|---|
| Estrategia de URL | Prefijo de idioma: `/en/...`, `/es/...` |
| Textos | Diccionario por idioma, cargado en el servidor |
| Slugs de ruta | **Traducidos** (`/game` vs `/juego`), mapeados en un único lugar |
| Slugs de entidad | Sin traducir. Un solo slug por entidad, en todos los idiomas |
| Idioma por defecto | Inglés, y es el `x-default` |

**Por qué traducir los slugs de ruta:** la URL es una señal de relevancia y el usuario
hispanohablante busca en español. **Por qué no traducir los slugs de entidad:** duplicar
identificadores multiplica las formas de referirse a la misma cosa y garantiza que se
desincronicen.

---

## Las tres reglas duras

Cada una nació de un problema real. Ver
[[2026-07-26 - Rutas localizadas duplicadas por locale cruzado]].

### 1. Una ruta localizada fija su idioma, no lo hereda

Con rutas traducidas bajo un segmento de idioma, el generador hace el **producto
cartesiano** y aparecen combinaciones que no existen conceptualmente pero sí como URLs
que devuelven 200.

Cada ruta traducida declara explícitamente a qué idioma pertenece al generar sus
parámetros. No alcanza con que la carpeta "sea la española".

**Pero ojo, hay dos casos y se arruinan en direcciones opuestas:**

| Caso | Carpetas | Qué emite cada una |
|---|---|---|
| Path **traducido** (`/privacy` vs `/privacidad`) | dos | **solo su propio idioma** |
| Path **idéntico** en ambos idiomas (`/faq`, `/cookies`) | una | **todos los idiomas** |

Cuando el path es igual en los dos idiomas, una sola carpeta sirve a ambos y tiene que
emitirlos a los dos. Si se le aplica la regla de "fijar el idioma" por reflejo, la
página desaparece en el otro idioma: el sitemap la anuncia y devuelve 404.

O sea: el error no es "heredar el idioma", es **no decidirlo**. Toda ruta declara
explícitamente en qué idiomas existe, sean uno o todos.

Aplica igual a rutas estáticas y dinámicas. Es fácil acordarse en las dinámicas
(`/juego/[slug]`) y olvidarlo en las estáticas, que tienen exactamente el mismo problema.

### 2. La canonical se deriva del contenido, no de la URL

Si el contenido es español, su canonical apunta a la URL española, sin importar bajo
qué prefijo llegó la petición.

Esto es defensa en profundidad: aunque se cuele una combinación inválida, no compite
por el mismo lugar. La regla 1 evita que la URL exista; la 2 la neutraliza si existe.

### 3. Un mapa de rutas, una sola fuente de verdad

Los pares de rutas traducidas viven en **un único lugar** del que se derivan la
navegación, el conmutador de idioma, el sitemap y los alternates. Cuando el mapa está
duplicado, una copia se desactualiza y aparecen enlaces internos rotos o URLs fuera
del sitemap.

---

## hreflang

- Cada página lista **todas** sus variantes de idioma, incluida sí misma.
- `x-default` siempre presente, apuntando al idioma por defecto.
- URLs **absolutas**. Una canonical o un hreflang relativo es un duplicado esperando.
- Recíproco: si A declara a B, B tiene que declarar a A. Si no, Google ignora el par.
- **Contenido que existe en un solo idioma no lleva alternates.** Un artículo escrito
  solo en español no tiene gemelo inglés; inventarle uno es peor que no declarar nada.

---

## La raíz del dominio

Una página raíz cuyo único contenido es elegir idioma es la URL más fuerte del dominio
gastada en cero contenido, y mete un clic entre el usuario y lo que buscaba.

Las opciones sanas son redirigir por idioma del navegador, o servir el idioma por
defecto directamente en la raíz. Si la raíz **existe** como selector, al menos que no
compita: no llevarla al tope de prioridad del sitemap.

*(Pendiente de resolver en mis sitios: hoy tengo selectores de idioma en la raíz con
prioridad 1.0.)*

---

## Contenido, no solo interfaz

Traducir la interfaz y dejar el contenido en inglés no genera una versión en español:
genera una página mayormente duplicada con los botones traducidos. Si el contenido no
se traduce de verdad, no vale la pena publicar esa variante.

---

## Enlaces

[[Programmatic SEO]] · [[NextJS]] · [[SEO Launch]] · [[Folder Structure]]
