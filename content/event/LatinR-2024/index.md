---
title: Creación de reportes de proyectos de iNaturalist usando `quarto`

event: 'LatinR2024: Conferencia Latinoamericana sobre Uso de R en Investigación + Desarrollo'
event_url: 'https://bienflorencia.github.io/LatinR2024/'

location: Virtual - Uruguay
#address:
#  street: 450 Serra Mall
#  city: Stanford
#  region: CA
#  postcode: '94305'
#  country: United States

summary: 'Presentación en la sesión Modelos y aplicaciones medioambientales, en recursos naturales, geociencias e hidrología'
abstract: ''

# Talk start and end times.
#   End time can optionally be hidden by prefixing the line with `#`.
date: '2024-11-21T10:30:00Z'
#date_end: '2030-06-01T15:00:00Z'
all_day: false

# Schedule page publish date (NOT talk date).
publishDate: '2024-11-21T10:30:00Z'

authors:
  - admin

tags:
  - iNaturalsit
  - community-science
  - NaturalistaUY

# Is this a featured talk? (true/false)
featured: false

#image:
#  caption: 'Image credit: [**Unsplash**](https://unsplash.com/photos/bzdhc5b3Bxs)'
#  focal_point: Right

links:
  - name: Repo
    icon: github
    icon_pack: fab
    url: 'https://github.com/bienflorencia/LatinR2024'


url_code: ''
url_pdf: ''
url_slides: 'https://bienflorencia.github.io/LatinR2024/slides/Grattarola_LatinR2024_slides.html#/title-slide'
url_video: 'https://youtu.be/lfpzEnZlUrc'

# Markdown Slides (optional).
#   Associate this talk with Markdown slides.
#   Simply enter your slide deck's filename without extension.
#   E.g. `slides = "example-slides"` references `content/slides/example-slides.md`.
#   Otherwise, set `slides = ""`.
slides: ""

# Projects (optional).
#   Associate this post with one or more of your projects.
#   Simply enter your project's folder or file name without extension.
#   E.g. `projects = ["internal-project"]` references `content/project/deep-learning/index.md`.
#   Otherwise, set `projects = []`.
projects: []

---

# Resumen

## Proyectos en iNaturalist

iNaturalist es una plataforma global online que permite a las personas cargar sus registros de seres vivos (o rastros de los mismos) e identificarlos mediante un proceso de evaluación comunitaria. Una de las grandes funcionalidades de esta plataforma es la creación de 'proyectos' que permiten agrupar observaciones bajo diferentes criterios de selección. Por ejemplo, por grupo taxonómico (insectos, aves, hongos), localización geográfica (Uruguay, provincia de Entre Ríos o Parque Nacional Torres del Paine), o una combinación de estas variables (mariposas del Uruguay). También se pueden generar proyectos por períodos de tiempo específicos, por ejemplo, que reúnan los registros tomados durante la primavera del sur, como lo hace el proyecto anual 'Gran Biobúsqueda del Sur' (GBS) o la última semana de mayo en ciudades del mundo como el 'Reto Naturalista Urbano'. Este trabajo busca generar un template o plantilla para el reporte de resultados de proyectos en iNaturalist.

## ¿Por qué hacer una plantilla para el reporte de proyectos?

Cada proyecto en iNaturalist cuenta con su propio identificador y URL, donde se muestran estadísticas básicas de las observaciones reunidas bajo el proyecto (sobre observaciones, especies y observadores). Sin embargo, a través de la plataforma no es sencillo extraer información más compleja que puede resultar de gran interés para quienes organizan y participan de los proyectos. Por ejemplo, en el marco del proyecto ¿se registró alguna especie nueva o rara para el lugar o para la plataforma?, ¿se registraron especies amenazadas o no nativas?, ¿en qué momento del día se cargaron más registros?, o ¿cuántas personas se unieron a la plataforma a través de este evento?.

## ¿Cómo funciona reportiNat?

Para crear el reporte usamos quarto y los paquetes lubridate, sf, tmap, geodata, wdpar, knitr y tidyverse (entre otros), y para obtener los datos usamos la API de iNaturalist (https://api.inaturalist.org) y los paquetes httr, y jsonlite para hacer las llamadas. Por el momento el código está adaptado a Uruguay, pero con algunos pocos cambios, podrá ser adaptado a cualquier país o región. Para generar el reporte se precisa indicar el código del proyecto generado en iNaturalist (ejemplo, gbs-2024-uruguay). Luego, se debe compilar el archivo quarto (marcando Render o en la terminal como quarto render reporte.qmd). Al compilar el archivo, se correrán diferentes chunks de código que permitirán descargar los datos, generar estadísticas básicas de quienes participaron del proyecto y reportar la cobertura taxonómica, espacial y temporal de los datos. Como salida se genera un archivo html con todos los resultados, incluyendo estadísticas, tablas y figuras.

## Casos de ejemplo

Una versión preliminar de este reporte puede verse en https://github.com/bienflorencia/LatinR2024, para dos casos de ejemplo, la [Gran Biobúsqueda del Sur del 2024 en Uruguay](https://github.com/bienflorencia/LatinR2024/blob/master/LatinR2024/code/example_GBS2024) y el projecto [Biodiversidad de Punta Ballena](https://github.com/bienflorencia/LatinR2024/blob/master/LatinR2024/code/example_punta_ballena).

## ¿Cómo se puede colaborar a mejorar reportiNat?

Para poder mejorar y enriquecer el reporte, el aporte de las comunidades de usuarios/as de iNaturalist en América Latina y LatinR es sumamente importante. Por el momento la plantilla es un html, pero ¿cuál sería el mejor formato a presentar el reporte? ¿debería ser un dashboard? ¿tiene potencial para ser un paquete de R?

Podés dejarme tus comentarios/sugerencias acá: [Discussion](https://github.com/bienflorencia/LatinR2024/discussions/1). ¡Gracias!
