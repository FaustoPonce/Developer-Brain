---
tags: [checklist, seo, launch]
updated: 2026-07-26
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
      no del parámetro de la URL.
- [ ] `hreflang` recíproco y con `x-default`. Contenido de un solo idioma, sin alternates.
- [ ] `lastmod` refleja cambios de contenido, **nunca la fecha del build**.
- [ ] Sin `noindex` accidental en producción.
- [ ] Ningún dato inventado en metadatos ni en datos estructurados: sin constantes de
      relleno, sin valores por defecto plausibles cuando falta el dato real.
- [ ] Solo entran al sitemap las páginas con contenido propio. Las flacas existen
      pero no se ofrecen.
- [ ] Abrir dos páginas generadas al azar del mismo tipo y leerlas en paralelo.
      Si se leen igual salvo el nombre, no se lanza.

## Importantes

- [ ] Título y meta descripción propios por página, dentro de los límites de caracteres.
- [ ] Un solo dominio canónico. El otro redirige 301.
- [ ] Página 404 propia, y ninguna URL indexada devolviendo 404.
- [ ] Datos estructurados validados con el test de resultados enriquecidos.
- [ ] Imagen Open Graph que existe y carga.
- [ ] `robots.txt` y sitemap accesibles y enviados a Search Console.
- [ ] La raíz del dominio no es un selector de idioma sin contenido —
      o al menos no está en prioridad 1.0.
- [ ] Enlazado interno real: ninguna página generada queda huérfana.

## Deseables

- [ ] Lighthouse móvil > 90 en Performance y SEO.
- [ ] Aviso a los buscadores (IndexNow) cuando hay URLs nuevas.
- [ ] Breadcrumbs con sus datos estructurados.
- [ ] Prioridades del sitemap coherentes entre sí, no todo en 1.0.

---

## Después de publicar

- [ ] A los 7 días: revisar cobertura en Search Console.
- [ ] Mirar específicamente "rastreada, actualmente sin indexar" y "duplicada".
      Ahí es donde aparecen los dos errores que más me costaron.

---

**Origen de los ítems:**
[[2026-07-26 - Rutas localizadas duplicadas por locale cruzado]] ·
[[2026-07-26 - Plantillas fijas de contenido = thin content]] ·
[[2026-07-26 - lastmod dinamico quema la confianza de Google]] ·
[[2026-07-26 - Datos fabricados en schema y metadatos]] ·
[[2026-07-26 - Borrar contenido viejo genero 404 permanentes]]

**Ver también:** [[Programmatic SEO]] · [[Internationalization]] · [[NextJS]]
