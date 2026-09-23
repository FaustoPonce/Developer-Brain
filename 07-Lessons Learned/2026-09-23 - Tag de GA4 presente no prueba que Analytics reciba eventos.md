---
tags: [lesson, analytics, ga4, consent, verification]
updated: 2026-09-23
severity: medio
---

# Tag de GA4 presente no prueba que Analytics reciba eventos

## Síntoma

Analytics muestra cero usuarios o el aviso de que no recibió datos, aunque el HTML de
producción contenga el Measurement ID y cargue `gtag.js`.

## Impacto

Se puede dar por configurada una medición que no tiene ningún evento utilizable. Sin
datos, las decisiones de SEO, contenido o conversión se toman a ciegas.

## Causa raíz

Que el tag se renderice solo prueba la instalación estática. No prueba el recorrido
que entrega el evento: el usuario puede no haber aceptado analíticas, un bloqueador
puede frenar la solicitud o la propiedad/flujo observado puede no ser el que corresponde
al ID publicado.

## Verificación

1. Confirmar que el Measurement ID del flujo de GA4 coincide con el publicado.
2. Abrir producción en un navegador sin bloqueadores, aceptar analíticas y navegar.
3. Comprobar la visita en **Realtime** antes de mirar los informes normales, que pueden
   tardar en procesarse.

Si Realtime no recibe la visita, entonces se inspecciona la solicitud del navegador; no
se cambia código por intuición.

## Cómo lo prevengo

La checklist de [[Deployment]] exige esta prueba de extremo a extremo para cada sitio
con GA4 y consentimiento.
