---
title: The scale-dependency of species associations in the eyes of integrated species distribution models

event: International Statistical Ecology Conference
event_url: 'https://chrissuthy.quarto.pub/international-statistical-ecology-conference/'

location: Swansea, UK
#address:
#  street: 450 Serra Mall
#  city: Stanford
#  region: CA
#  postcode: '94305'
#  country: United States

summary: Presentation at the ISEC 2024
abstract: ''

# Talk start and end times.
#   End time can optionally be hidden by prefixing the line with `#`.
date: '2024-07-17T08:15:00Z'
#date_end: '2030-06-01T15:00:00Z'
all_day: false

# Schedule page publish date (NOT talk date).
publishDate: '2024-07-17T08:15:00Z'

authors:
  - admin
  - Gurutzeta Guillera-Arroita
  - José Lahoz-Monfort
  - Petr Keil

tags:
  - simulation
  - integrated SDM
  - joint SDM

# Is this a featured talk? (true/false)
featured: false

#image:
#  caption: 'Image credit: [**Unsplash**](https://unsplash.com/photos/bzdhc5b3Bxs)'
#  focal_point: Right

#links:
#  - name: Abstract
#    url: https://esa2023.eventscribe.net/fsPopup.asp?PresentationID=1276990&query=grattarola&Mode=presInfo
url_code: ''
url_pdf: ''
url_slides: 'ISEC_2024_Grattarola_IJSDMs.pdf'
url_video: ''

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

## Session: 'Data integreation III'

Understanding species associations has become a key topic in the study of species distributions. Unfortunately, species occurrence data are typically available at a coarser grain than the one of species associations. We ask: can fine-scale associations be detected using coarse-grain data or a combination of different coarse-grain data types? We performed a simulation study to assess if we can recover species interactions at fine grain from data of varying spatial grains, combining ideas from two rising popular methods: integrated species distribution models (iSDMs) and joint species distribution models (jSDMs). We simulated two species that are jointly influenced by an environmental covariate and have varying levels of species-species associations (e.g., negative, zero and positive). We assumed an underlying Poisson point pattern and described the species associations with a covariance matrix (residual species–species correlations). We then drew presence-absence and abundance data at three different increasing grain sizes and modelled the species distribution aiming to recover the true parameters influencing the distributions (environment and species associations). We generated 10 replicates for each species-environment pair, grain size and species covariance and ran them in R with JAGS. Our preliminary findings show that as we increase the grain of the species data, we fail to recover the species association. However, our results indicate that we have not yet successfully captured the complexity of these interactions when using point patterns. This study serves as a foundation for future exploration as we plan to assess several modelling variants for data integration. Specifically, we will explore alternative models that can enable us to integrate data, such as downscaling species distribution models
