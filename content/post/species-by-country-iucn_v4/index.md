---
title: New way to get a list of species by country from IUCN
subtitle: Update to use IUCN API v4
summary: Update to use IUCN API v4
authors:
  - admin
tags:
  - R
  - IUCN
  - API

categories: []
projects: []
# Date published
date: '2026-02-08T00:00:00Z'
# Date updated
lastMod: '2026-02-08T00:00:00Z'
# Featured image
# Place an image named `featured.jpg/png` in this page's folder and customize its options here.
image:
  caption: ''
  focal_point: ''
  placement: 2
  preview_only: false
---

Getting a list of species per country according to the ICUN Red List, as
I did in [one of my previous
posts](https://flograttarola.com/post/species-by-country-iucn/), is not
possible anymore since the IUCN API has been upgraded to v4. Because of
this, the `rl_sp_country()` function from the
[rredlist](https://docs.ropensci.org/rredlist) R package has also been
deprecated, as it relied in the v3 of the API (see
[changelog]((https://docs.ropensci.org/rredlist/news/index.html#rredlist-100))).

So, how can we get the number of species in the country according to the
IUCN Red List with the new [IUCN API v4](https://api.iucnredlist.org)?
Here’s how I did it.

## Creating a function that uses IUCN API v4

The function queries the IUCN Red List v4 endpoint (and thus requires a
valid IUCN API token, `api_key`) to retrieve the number of species
associated with one or more ISO country codes (`country_codes`). It then
returns a tibble with one row per country code and a numeric `n_species`
column. If a country is not found or the request fails, `n_species` will
be `NA`.

``` r
getNspeciesPerCountry <- function(api_key, country_codes, verbose=FALSE) {
  
  # Define the base URL for the IUCN Red List API
  base_url <- "https://api.iucnredlist.org/api/v4"
  endpoint <- str_glue("/countries")
  
  # Authorization token
  headers <- add_headers(Authorization = paste("Bearer", api_key))
  
  results <- tibble(country_code = character(),
                    n_species = integer())
  
  for (country_code in country_codes) {
    if(verbose == TRUE){
      cat("Fetching data for:", country_code, "\n")  
    }
    
    api_url <- str_glue('{base_url}{endpoint}/{country_code}?latest=true&scope_code=1')
    response <- HEAD(api_url, headers)
    
    if (status_code(response) == 200) {
      n_species <- response$headers$`total-count`
    } else {
      print("Country not found.")
      n_species <- NA
    }
    
    results_i <- tibble(country_code = country_code,
                        n_species = as.numeric(n_species))
    if (!is.null(results_i)) {
      results <- bind_rows(results, results_i)
    }
  }
  return(results)
}
```

## Using it

Let’s do a quick example with Argentina (`AR`), Brazil (`BR`), and
Uruguay (`UY`), and an invented country code `XX`, to test the function.

``` r
library(knitr)
library(countrycode)
library(httr)
library(jsonlite)
library(tidyverse)

# Your IUCN Red List API key
IUCN_REDLIST_KEY <- Sys.getenv("IUCN_REDLIST_KEY")
getNspeciesPerCountry(api_key = IUCN_REDLIST_KEY, 
                      country_codes= c('AR', 'BR', 'UY', 'XX'),
                      verbose = TRUE)
```

    Fetching data for: AR 
    Fetching data for: BR 
    Fetching data for: UY 
    Fetching data for: XX 
    [1] "Country not found."

    # A tibble: 4 × 2
      country_code n_species
      <chr>            <dbl>
    1 AR                4807
    2 BR               19891
    3 UY                2001
    4 XX                  NA

## Same example as previous post

Now that we know it works, let’s test it on the same example as my
previous post.

``` r
LatinAmerica <- tibble(country= c('Mexico', 'Brazil', 'Costa Rica', 'Colombia', 'Peru', 'Argentina', 'Ecuador', 'Panama', 'Chile', 'Venezuela', 'Belize', 'Honduras', 'Bolivia', 'Guatemala', 'Cuba', 'Nicaragua', 'Paraguay', 'Bahamas', 'Jamaica', 'Trinidad and Tobago', 'Guyana', 'Dominican Republic', 'El Salvador', 'Suriname', 'Uruguay', 'Haiti')) %>%
  mutate(country_code=countrycode(country, 'country.name', 'iso2c'))

LatinAmericaNspecies <- getNspeciesPerCountry(api_key = IUCN_REDLIST_KEY, 
                                              country_codes = LatinAmerica$country_code)

LatinAmerica <- left_join(LatinAmerica, LatinAmericaNspecies)
LatinAmerica %>% kable()
```

| country             | country_code | n_species |
|:--------------------|:-------------|----------:|
| Mexico              | MX           |     12904 |
| Brazil              | BR           |     19891 |
| Costa Rica          | CR           |      7633 |
| Colombia            | CO           |     16119 |
| Peru                | PE           |     11275 |
| Argentina           | AR           |      4807 |
| Ecuador             | EC           |     12169 |
| Panama              | PA           |      7839 |
| Chile               | CL           |      2412 |
| Venezuela           | VE           |     11219 |
| Belize              | BZ           |      3934 |
| Honduras            | HN           |      5862 |
| Bolivia             | BO           |      7440 |
| Guatemala           | GT           |      6141 |
| Cuba                | CU           |      4064 |
| Nicaragua           | NI           |      5652 |
| Paraguay            | PY           |      2636 |
| Bahamas             | BS           |      2559 |
| Jamaica             | JM           |      2828 |
| Trinidad and Tobago | TT           |      3202 |
| Guyana              | GY           |      5893 |
| Dominican Republic  | DO           |      3037 |
| El Salvador         | SV           |      3134 |
| Suriname            | SR           |      4811 |
| Uruguay             | UY           |      2001 |
| Haiti               | HT           |      3042 |

### And, that’s all!
