+++
title = "Ciencia abierta y cultura libre en Uruguay"
date = 2021-11-11T00:00:00  # Schedule page publish date.
draft = false

# Talk start and end times.
#   End time can optionally be hidden by prefixing the line with `#`.
time_start = 2021-11-11T10:15:00
#time_end = 2030-06-01T15:00:00

# Authors. Comma separated list, e.g. `["Bob Smith", "David Jones"]`.
authors = ["Florencia Grattarola", "Juan Manuel Barreneche"]

# Abstract and optional shortened version.
abstract = ""
abstract_short = "Sesión Datos Espaciales - LatinR2021"

# Name of event and optional event URL.
event = "LatinR2021: Conferencia Latinoamericana sobre Uso de R en Investigación + Desarrollo"
event_url = "https://latin-r.com"

# Location of event.
location = "Virtual"

# Is this a selected talk? (true/false)
selected = true

# Projects (optional).
#   Associate this talk with one or more of your projects.
#   Simply enter your project's folder or file name without extension.
#   E.g. `projects = ["deep-learning"]` references
#   `content/project/deep-learning/index.md`.
#   Otherwise, set `projects = []`.
projects = ["julana", "biodiversidata"]

# Tags (optional).
#   Set `tags = []` for no tags, or use the form `tags = ["A Tag", "Another Tag"]` for one or more tags.
tags = ["open data", "open-science", "citizen-science", "NaturalistaUY"]

# Slides (optional).
#   Associate this talk with Markdown slides.
#   Simply enter your slide deck's filename without extension.
#   E.g. `slides = "example-slides"` references
#   `content/slides/example-slides.md`.
#   Otherwise, set `slides = ""`.
#slides = "example-slides"

# Links (optional).
url_pdf = ""
url_slides = ""
url_video = "https://youtu.be/8qltR0p_Czg"
url_code = ""

# Does the content use math formatting?
math = true

# Featured image
# To use, add an image named `featured.jpg/png` to your page's folder.
[image]
  # Caption (optional)
  caption = ""

  # Focal point (optional)
  # Options: Smart, Center, TopLeft, Top, TopRight, Left, Right, BottomLeft, Bottom, BottomRight
  focal_point = "Smart"

+++

# Soy naturalista y quiero pasear en mi país, ¿dónde hay más oportunidades de llenar vacíos de información?

La biodiversidad a nivel global está disminuyendo a un ritmo sin precedentes. En este contexto, uno de los principales desafíos que enfrentan los países alrededor del mundo es poder medir cuantitativamente la biodiversidad y monitorear sus cambios. Sin embargo, en diversas regiones de Latinoamérica y el Caribe los datos de biodiversidad disponibles de manera abierta son limitados. Para poder tomar mejores decisiones basadas en evidencia es sumamente crítico revertir la falta de datos primarios sobre la distribución geográfica de las especies. Para esto, la ciencia ciudadana se presenta como una herramienta comunitaria transformadora. Una de las plataformas globales más usadas en Latinoamérica y el mundo es iNaturalist ([inaturalist.org](https://www.inaturalist.org)). Esta plataforma web y aplicación para celulares tiene como principal funcionalidad el registro de organismos en el tiempo y el espacio y reúne, en torno a estos datos, a la comunidad de naturalistas más grande del mundo. Una característica de los datos que provienen de la ciencia ciudadana es que suelen centrarse en áreas de fácil acceso y próximas a centros poblados, carreteras y áreas de interés (como por ejemplo, áreas protegidas). Para poder generar datos más diversos y mejorar el conocimiento de la biodiversidad, quienes observan (y quienes usan los datos) se verían muy beneficiados de tener a disposición una herramienta que les permita decidir a dónde ir a registrar organismos y qué grupos observar en función de maximizar su aporte.    

Usando como base los datos ingresados en la plataforma iNaturalist para Uruguay (descargados el 21 de octubre de 2021), nos propusimos generar un mapa interactivo que ordene las áreas con déficit de datos de biodiversidad y nos permita resaltar aquellas en las que registros adicionales de biodiversidad podrían ser particularmente valiosos para llenar los vacíos de conocimiento. Una vez descargados los datos, filtramos los registros de organismos cautivos o cultivados, los que no habían sido identificados con grado de investigación y aquellos con coordenadas obscurecidas, y nos quedamos con las observaciones identificadas a nivel taxonómico de especie, pasando de 28,215 registros a 17,398 (61.7% del total). Luego, dividimos el país en grillas hexagonales de 500km^2^ y calculamos una serie de métricas por grilla (intensidad espacial y temporal), usando los paquetes `geouy`, `sf` y `tidyverse`, para luego integrarlas en una medida de prioridad. Intensidad espacial fue calculada como la cantidad de registros por unidad de área, e intensidad temporal como la cantidad de registros por cada mes/año de registro. Ambas métricas fueron re-escaladas entre 0 y 1, sumadas y vueltas a re-escalar para generar nuestro índice de prioridad. Finalmente, las grillas fueron categorizados como de prioridad ‘Muy Alta’, ‘Alta’, ‘Media’, ‘Baja’, ‘Muy Baja’, y ‘Sin registros’, en función de cada grupo taxonómico. El sistema de puntaje de prioridad construido, no es absoluto sino relativo al conjunto de celdas. De esta manera, siempre será posible encontrar celdas con mayor o menor oportunidad de hacer aportes valiosos. Los datos fueron luego usados como base para crear una app en Shiny, usando los paquetes `shiny` y `leaflet`. La app permite visualizar el mapa de Uruguay con las grillas hexagonales coloreadas según el orden de prioridad generado (ver figura, abajo). Además, quienes consulten el mapa podrán seleccionar el grupo taxonómico de interés y elegir una grilla, para la cual se desplegará información sobre: el área de la grilla, los valores de intensidad espacial y temporal, la cantidad de especies registradas para esa grilla, el número de especies nuevas registradas en el último año y la proporción de éstas sobre el total registrado para esa grilla.  

Haciendo uso de esta herramienta, usuarios y usuarias de la plataforma iNaturalist podrán decidir el destino de sus paseos en función de dónde registrar observaciones de la biodiversidad es más urgente y así contribuir a mejorar el conocimiento sobre la distribución de especies en el país. Esta herramienta podría replicarse en otras partes del mundo, además, nuevas métricas podrían ser propuestas y formas de visualizar los datos acordes a las regiones (e.g. resaltando rutas, áreas administrativas o zonas de interés). Esta es la primera vez que hacemos pública la herramienta por lo que esperamos poder recibir devoluciones para seguirla mejorando a futuro.  

## Enlaces

- [Demo de la aplicación en vivo](https://bienflorencia.shinyapps.io/iNatUy_priority_map/)
- [Diapositivas de la presentación (archivo HTML creado con xaringan)](https://github.com/bienflorencia/LatinR2021/blob/ffffbfc952ced0309486ce71b0525e2bd30570c3/docs/xaringan_latinR/Presentacion_NaturalistaUY.html)
- [Presentación en YouTube](https://www.youtube.com/watch?v=8qltR0p_Czg)
- [Carpeta de la aplicación](https://github.com/bienflorencia/LatinR2021/tree/main/iNatUy_priority_map)
