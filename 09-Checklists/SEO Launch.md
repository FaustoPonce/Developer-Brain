---
tags: [checklist, seo, launch]
updated: 2026-08-21
---

# SEO Launch

**Cuándo:** antes de que Google vea el sitio, y después de cualquier cambio de rutas.
**Tiempo:** ~30 minutos.

Cada ítem de acá está porque ya me costó tráfico. El origen está enlazado al final.

---

## Automático primero

Si el proyecto salió de la plantilla, esto cubre los 4 ítems más caros de la lista:

```
npm run check
```

Falla si el sitemap anuncia URLs sin página, si hay duplicados por locale cruzado,
si el `lastmod` parece la fecha del build, o si falta alguna canonical o es relativa.
**Lo que sigue es lo que ninguna herramienta puede chequear.**

## Bloqueantes

- [ ] Cada ruta declara **explícitamente en qué idiomas existe**. Path traducido ⇒ solo
      su idioma; path idéntico en ambos ⇒ los dos. Ver [[Internationalization]].
- [ ] Canonical **absoluta**, única, y derivada de la identidad del contenido —
      no del parámetro de la URL. Verificar con un grep sobre **todas** las rutas del
      proyecto, no solo las que motivan la sesión — las páginas de trámite (legal,
      contacto, 404) son las que más se saltean.
      Ver [[2026-08-15 - Regla de canonical cumplida solo en las paginas vistosas]].
- [ ] `hreflang` recíproco y con `x-default`. Contenido de un solo idioma, sin alternates.
- [ ] `lastmod` refleja cambios de contenido, **nunca la fecha del build**.
- [ ] Sin `noindex` accidental en producción.
- [ ] Ningún dato inventado en metadatos ni en datos estructurados: sin constantes de
      relleno, sin valores por defecto plausibles cuando falta el dato real.
- [ ] Ninguna reseña/rating de un tercero emitida como `aggregateRating` propio. Si hay
      que inflar el `ratingCount` para que valide, el dato no es tuyo y el markup no va.
      Ver [[2026-07-26 - Datos fabricados en schema y metadatos]].
- [ ] Solo entran al sitemap las páginas con contenido propio. Las flacas existen
      pero no se ofrecen.
- [ ] Si el proyecto usa `trailingSlash: true`: `curl -sI` sobre una muestra de URLs
      del sitemap (y de `llms.txt` si existe) — tienen que responder 200, no 3xx.
      `<Link>` normaliza el trailing slash solo en rutas 100% dinámicas; `sitemap.ts`,
      `llms.ts`, JSON-LD y cualquier `<a>` plana no pasan por esa normalización nunca.
      Ver [[2026-08-16 - trailingSlash no se propaga fuera de Link]].
- [ ] Si el proyecto salió de `Pages-Template`: **sin datos de ejemplo del scaffold
      en el dataset de producción** (`brand: "Ejemplo"` o el marcador que use el
      template). No asumir que ya se borraron sin correr un grep.
- [ ] `robots.txt` no bloquea la carpeta de assets estáticos del framework
      (`/_next/` en Next.js). Google necesita esos archivos para renderizar la página.
- [ ] Abrir dos páginas generadas al azar del mismo tipo y leerlas en paralelo.
      Si se leen igual salvo el nombre, no se lanza.
- [ ] Revisar a mano una muestra del 5-10% de las páginas generadas. Ninguna
      herramienta detecta "esto se lee como plantilla".
- [ ] Aplicar el test a una página cualquiera: **¿valdría la pena publicarla si
      ninguna de sus hermanas existiera?** Ver [[Programmatic SEO]].
- [ ] Si es un lanzamiento grande: **publicar en tandas de 50-100 y esperar 2-4
      semanas** mirando indexación antes de ampliar. No soltar 500+ de una vez.

## Importantes

- [ ] Título y meta descripción propios por página, dentro de los límites de caracteres.
- [ ] Un solo dominio canónico. El otro redirige 301.
- [ ] Página 404 propia **verificada sirviéndola**, no solo el archivo existiendo:
      `curl` a una URL rota real (o al build exportado) y confirmar que el HTML es
      el propio. Con `output: 'export'` + `[lang]/layout.tsx` como raíz, un
      `not-found.tsx` dentro de `[lang]/` nunca se sirve — solo el de la raíz
      literal de `app/`. `next dev` no lo detecta. **Si el fix agrega un
      `app/layout.tsx` (raíz) nuevo por encima de un layout que ya declaraba
      `<html>`, no alcanza con `grep` sobre el HTML exportado** — puede romper la
      hidratación en el cliente sin que el HTML estático lo muestre. Probar con
      Playwright real (consola + interactividad post-hidratación).
      Ver [[2026-08-18 - not-found.tsx fuera de la raiz nunca se sirve en export estatico]]
      y [[2026-08-21 - Verificar HTML estatico no alcanza para confirmar que un layout raiz es seguro]].
      Y ninguna URL indexada devolviendo 404.
- [ ] Datos estructurados validados con el test de resultados enriquecidos.
- [ ] Imagen Open Graph que existe y carga.
- [ ] `robots.txt` y sitemap accesibles y enviados a Search Console.
- [ ] Si la raíz es un selector de idioma: `x-default` la señala a **ella misma**
      (es el uso que Google documenta para esa etiqueta, no un error). Lo único a
      decidir es si conviene igual saltear el clic con un 301 a la versión por
      defecto — es UX/equity de enlaces, no una regla de indexación.
      Ver [[2026-08-14 - Selector de idioma en la raiz no es antipatron de Google]].
- [ ] Sin scroll horizontal en 320/360/375/390/412/430px, sobre todo en lo que vive
      en el layout raíz (header/footer): un bug ahí no es de una página, es de todas
      las que lo compartan. Medir con Playwright (`scrollWidth - clientWidth`), no
      angostando la ventana del navegador a ojo.
      Ver [[2026-08-16 - Header compartido sin breakpoint mobile pasa cualquier chequeo automatico]].
- [ ] Enlazado interno real: ninguna página generada queda huérfana.
- [ ] El enlazado interno y las prioridades del sitemap **empujan primero lo que más
      vale**, no reparten parejo. Dominio nuevo = presupuesto de rastreo chico.
- [ ] Señales de Trust visibles: contacto, política de privacidad real, y quién hace
      el contenido. Es lo más barato de E-E-A-T y lo que si falta hunde el resto.
      Ver [[E-E-A-T]].
- [ ] La respuesta principal de cada página está **arriba**, en bloque autocontenido.
      ~44% de las citas de IA salen del primer 30% de la página.
      Ver [[Generative Engine Optimization]].

## Deseables

- [ ] Lighthouse móvil > 90 en Performance y SEO.
- [ ] Aviso a los buscadores (IndexNow) cuando hay URLs nuevas.
- [ ] Breadcrumbs con sus datos estructurados.
- [ ] Prioridades del sitemap coherentes entre sí, no todo en 1.0.
- [ ] `llms.txt` / `llms-full.txt` — **Google los ignora explícitamente.** Solo por si
      sirven a asistentes que no son de Google. Último de la lista, no antes.
      Ver [[AI Crawler Discoverability]].

---

## Después de publicar

- [ ] A los 7 días: revisar cobertura en Search Console.
- [ ] Mirar específicamente "rastreada, actualmente sin indexar" y "duplicada".
      Ahí es donde aparecen los dos errores que más me costaron.
- [ ] Agendar un refresco de contenido. Una página sin tocar 6+ meses **pierde
      elegibilidad de cita en IA**, y el contenido de menos de 3 meses tiene ~3x más
      probabilidad de ser citado. Refrescar es cambiar el contenido, no la fecha.
      Ver [[Generative Engine Optimization]].
- [ ] Si el catálogo destaca un dato "vendible" (precio, specs) de forma prominente,
      revisar en Consultas de Search Console qué proporción de las impresiones vienen
      de consultas que **no mencionan el nicho**. Posición mala (30+) y cero clics en
      esas consultas es la señal de que el sitio compite fuera de su categoría real.
      Ver [[2026-08-13 - Precio destacado sin filtro de nicho diluye el posicionamiento]].

---

**Origen de los ítems:**
[[2026-08-21 - Verificar HTML estatico no alcanza para confirmar que un layout raiz es seguro]] ·
[[2026-08-18 - not-found.tsx fuera de la raiz nunca se sirve en export estatico]] ·
[[2026-08-16 - Header compartido sin breakpoint mobile pasa cualquier chequeo automatico]] ·
[[2026-08-16 - trailingSlash no se propaga fuera de Link]] ·
[[2026-07-26 - Rutas localizadas duplicadas por locale cruzado]] ·
[[2026-07-26 - Plantillas fijas de contenido = thin content]] ·
[[2026-07-26 - lastmod dinamico quema la confianza de Google]] ·
[[2026-07-26 - Datos fabricados en schema y metadatos]] ·
[[2026-07-26 - Borrar contenido viejo genero 404 permanentes]] ·
[[2026-08-07 - robots.txt bloqueando los assets estaticos del framework]] ·
[[2026-08-11 - Datos de ejemplo del template publicados como productos reales]] ·
[[2026-08-13 - Precio destacado sin filtro de nicho diluye el posicionamiento]] ·
[[2026-08-15 - Regla de canonical cumplida solo en las paginas vistosas]]

**Ver también:** [[Programmatic SEO]] · [[Internationalization]] · [[NextJS]]
