---
tags: [lesson, nextjs, adsense, verification]
updated: 2026-10-27
severity: medio
---

# Los verificadores externos pueden requerir el tag literal

## Síntoma

Un proveedor rechaza verificar la propiedad del sitio aunque el navegador carga su
cliente y el texto de la URL externa aparece en el HTML.

## Causa raíz

Un framework puede transformar un componente de scripts en un `preload` y una
instrucción interna que inserta el recurso durante la hidratación. Eso sirve para un
navegador, pero un verificador que busca el tag HTML exacto no necesariamente ejecuta
ni interpreta ese mecanismo.

## Regla

Cuando un tercero exige explícitamente un fragmento dentro de `<head>`, emitir ese
fragmento como tag HTML literal. No envolverlo en un cargador del framework salvo que
el tercero documente que lo soporta.

## Verificación

Inspeccionar el HTML servido, no el componente fuente: tiene que existir un
`<script src="…">` literal con los atributos que entrega el tercero. Después ejecutar
la verificación del proveedor contra producción.

## Prevención

Antes de integrar scripts de propiedad, analítica o monetización, distinguir dos
contratos: que el navegador ejecute el recurso y que el verificador externo pueda
reconocer el markup. Son pruebas diferentes.

## Enlaces

[[NextJS]]
