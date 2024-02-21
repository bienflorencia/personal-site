---
title: Get a list of species by country from IUCN
subtitle: How many species are in each country according to IUCN Red List?
summary: How many species are in each country according to IUCN Red List?
authors:
  - admin
tags:
  - R
  - IUCN
  - API

categories: []
projects: []
# Date published
date: '2024-02-21T00:00:00Z'
# Date updated
lastMod: '2024-02-21T00:00:00Z'
# Featured image
# Place an image named `featured.jpg/png` in this page's folder and customize its options here.
image:
  caption: ''
  focal_point: ''
  placement: 2
  preview_only: false
---

Today, my goal is to get a list of species per country according to the
ICUN Red List. I could use the
[rredlist](https://docs.ropensci.org/rredlist) R package. There is a
function that sounds helpful: `rl_sp_country()`. But just to test my API
query techniques, I will use the [IUCN
API](https://apiv3.iucnredlist.org/api/v3/docs) to find it out. Note:
the `rredlist` package, does exactly the same, an API query.

You can check my other posts playing around with the **iNaturalist**
API: [First steps with the iNaturalist
API](https://flograttarola.com/post/inat_api/) and [Using the
iNaturalist API to detect novel
records](https://flograttarola.com/post/inat_api_new_records/).

### Let’s go!

The use of the IUCN API is strictly under the [Terms of Use of the IUCN
Red List of Threatened
Species](http://www.iucnredlist.org/info/terms-of-use).

``` r
library(knitr)
library(countrycode)
library(rredlist)
library(httr)
library(jsonlite)
library(tidyverse)
```

We will use data from Latin America and the package `countrycode` to get
the *ISO2* code for each country.

``` r
LatinAmerica <- tibble(country= c('Mexico', 'Brazil', 'Costa Rica', 'Colombia', 'Peru', 'Argentina', 'Ecuador', 'Panama', 'Chile', 'Venezuela', 'Belize', 'Honduras', 'Bolivia', 'Guatemala', 'Cuba', 'Nicaragua', 'Paraguay', 'Bahamas', 'Jamaica', 'Trinidad and Tobago', 'Guyana', 'Dominican Republic', 'El Salvador', 'Suriname', 'Uruguay', 'Haiti')) %>%
  mutate(iso=countrycode(country, 'country.name', 'iso2c'))

LatinAmerica %>% kable()
```

| country             | iso |
|:--------------------|:----|
| Mexico              | MX  |
| Brazil              | BR  |
| Costa Rica          | CR  |
| Colombia            | CO  |
| Peru                | PE  |
| Argentina           | AR  |
| Ecuador             | EC  |
| Panama              | PA  |
| Chile               | CL  |
| Venezuela           | VE  |
| Belize              | BZ  |
| Honduras            | HN  |
| Bolivia             | BO  |
| Guatemala           | GT  |
| Cuba                | CU  |
| Nicaragua           | NI  |
| Paraguay            | PY  |
| Bahamas             | BS  |
| Jamaica             | JM  |
| Trinidad and Tobago | TT  |
| Guyana              | GY  |
| Dominican Republic  | DO  |
| El Salvador         | SV  |
| Suriname            | SR  |
| Uruguay             | UY  |
| Haiti               | HT  |

Now, let’s **build a function** to get the list of species per country.

The function gets a list of countries iso codes and a IUCN token, and
returns a tibble with the scientific name and taxon id of the species,
the IUCN category and the country iso. We will use the packages `httr`
and `jsonlite` to process the *JSON* call. To check that is doing what
we want, the function will print on screen the process, with the result
number and the queried country.

``` r
getSpeciesByCountriesIUCN <- function(country_iso, token) {

  speciesByCountries <- tibble(scientific_name = character(),
                               taxon_id = numeric(),
                               category = character(),
                               country = character())

  num_results = 0 # used to put the API to sleep and print on the console the num

  for(country_iso_i in country_iso) {  

    if ((num_results %% 10) + 10 == 10) {
      Sys.sleep(0) # every 10 calls, the code can stop for x seconds
    }

    call_url <- str_glue('https://apiv3.iucnredlist.org/api/v3/country/getspecies/',
                         '{country_iso_i}?token={token}')

    get_json_call <- GET(url = call_url) %>%
      content(as = "text") %>% fromJSON(flatten = TRUE)

    results <- as_tibble(get_json_call$result)

    speciesByCountries_i <- tibble(scientific_name = results$scientific_name,
                                   taxon_id = results$taxonid,
                                   category = results$category,
                                   country = country_iso_i)

    speciesByCountries <- rbind(speciesByCountries, speciesByCountries_i)
    num_results <- num_results + 1
    cat(num_results, ': ', country_iso_i, '\n')
  }
  return(speciesByCountries)
}
```

### So, let’s try it out!

To use it you **WILL** need a token from IUCN. Here you can find more
information on how to get it.
<https://apiv3.iucnredlist.org/api/v3/token>.

``` r
token <- '' # paste here the token you get from IUCN
```

``` r
speciesByLatamCountriesIUCN <- getSpeciesByCountriesIUCN(LatinAmerica$iso, token)
```

``` r
head(speciesByLatamCountriesIUCN, n=5) %>%
  kable()
```

| scientific_name    |  taxon_id | category | country |
|:-------------------|----------:|:---------|:--------|
| Abaeis nicippe     | 173005000 | LC       | MX      |
| Abarema idiopoda   | 146784206 | LC       | MX      |
| Abarema zolleriana | 198888990 | VU       | MX      |
| Abatia mexicana    | 126620170 | VU       | MX      |
| Abeillia abeillei  |  22687170 | LC       | MX      |

Finally, let’s do some summaries to find out how many species we have
per country, and, for instance, how many are not threatened according to
the IUCN categories (`LC`), (`VU`), and (`NT`), and the percentage over
the total.

``` r
speciesByLatamCountriesIUCN %>%
  mutate(countryName=countrycode(country, 'iso2c', 'country.name')) %>%
  group_by(countryName) %>%
  summarise(species_richness=n_distinct(scientific_name),
            non_threatened=n_distinct(scientific_name[category == 'LC' |
                                                         category == 'VU'|
                                                         category ==  'NT'])) %>%
  ungroup() %>%
  mutate(`%` = scales::label_percent()(round(non_threatened / species_richness, 2))) %>%
  rename(`Country name`=countryName,
         `Number of species`=species_richness,
         `Number of non-threatened species`=non_threatened) %>%
  kable(format.args= list(big.mark = ','))
```

| Country name       | Number of species | Number of non-threatened species | %   |
|:-------------------|------------------:|---------------------------------:|:----|
| Argentina          |             4,587 |                            4,146 | 90% |
| Bahamas            |             2,516 |                            2,299 | 91% |
| Belize             |             3,750 |                            3,522 | 94% |
| Bolivia            |             6,536 |                            6,057 | 93% |
| Brazil             |            18,079 |                           14,910 | 82% |
| Chile              |             2,382 |                            1,976 | 83% |
| Colombia           |            14,570 |                           12,628 | 87% |
| Costa Rica         |             7,455 |                            6,734 | 90% |
| Cuba               |             3,459 |                            3,014 | 87% |
| Dominican Republic |             2,870 |                            2,562 | 89% |
| Ecuador            |            11,343 |                            9,087 | 80% |
| El Salvador        |             3,061 |                            2,872 | 94% |
| Guatemala          |             5,880 |                            5,194 | 88% |
| Guyana             |             5,211 |                            4,935 | 95% |
| Haiti              |             2,870 |                            2,410 | 84% |
| Honduras           |             5,667 |                            5,154 | 91% |
| Jamaica            |             2,702 |                            2,324 | 86% |
| Mexico             |            12,564 |                           10,002 | 80% |
| Nicaragua          |             5,492 |                            5,150 | 94% |
| Panama             |             7,645 |                            6,822 | 89% |
| Paraguay           |             2,475 |                            2,363 | 95% |
| Peru               |            10,169 |                            8,981 | 88% |
| Suriname           |             4,412 |                            4,220 | 96% |
| Trinidad & Tobago  |             3,075 |                            2,868 | 93% |
| Uruguay            |             1,901 |                            1,742 | 92% |
| Venezuela          |            10,117 |                            8,953 | 88% |

### That’s all!
