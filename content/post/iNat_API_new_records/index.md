---
title: Using the iNaturalist API to detect novel records
subtitle: How many never-before recorded species did we have in Uruguay in 2023?
summary: How many never-before recorded species did we have in Uruguay in 2023?
authors:
  - admin
tags:
  - R
  - iNaturalist
  - API

categories: []
projects: []
# Date published
date: '2024-01-28T00:00:00Z'
# Date updated
lastMod: '2024-01-28T00:00:00Z'
# Featured image
# Place an image named `featured.jpg/png` in this page's folder and customize its options here.
image:
  caption: ''
  focal_point: ''
  placement: 2
  preview_only: false

links:
  - name: NaturalistaUY
    icon: binoculars
    icon_pack: fa
    url: https://www.naturalista.uy
  - name: iNat API
    icon: inaturalist
    icon_pack: ai
    url: https://api.inaturalist.org/v1/docs/
---

This time, I’m interested in detecting if any of the records uploaded to
iNaturalist in Uruguay during the past year, 2023, belong to new species
for NaturalistaUY (our national site) or for the iNaturalist global
platform. To answer this, I once again explored the [iNaturalist
API](https://api.inaturalist.org/v1/docs/). If you want to know more
about my first experiment with the API, see my previous post [How many
users in NaturalistaUY are
Uruguayan?](https://flograttarola.com/post/inat_api/).

So, I need to know if any record is:

-   **new for iNaturalist**, i.e., a species recorded in Uruguay in 2023
    that has no previous records in the platform.
-   **new for NaturalistaUY**: i.e., a species recorded in Uruguay in
    2023 that has been recorded before in the platform, but has no
    previous records in Uruguay.

The API has a call for
[taxa](https://api.inaturalist.org/v1/docs/#/Taxa) which takes a list of
species or ids (`taxon_id`) and returns information about them, such as
taxonomic name and, lucky for us, the number of observations the taxa
has (`observations_count`). We will use this to get the numbers for
‘species’ (as taxon rank) we had in Uruguay during 2023.

To do it, we first create the function `getiNatTaxonRank` which takes a
`taxon_id` as an argument, and returns the `taxon_name`, `taxon_rank`
(e.g., species, family, subspecies), and `observations_count` for the
taxon.

``` r
library(httr)
library(jsonlite)
library(knitr)
library(tidyverse)

getiNatTaxonRank <- function(taxon_id){

  taxaRanks <- tibble(taxon_name = character(),
                      taxon_id = numeric(),
                      taxon_rank = character(),
                      observations_count = numeric())

  num_results = 0 # used to put the API to sleep and print on the console the num

  for (taxon_id_i in taxon_id) {  

    if ((num_results %% 10) + 10 == 10) {
      Sys.sleep(10) # every 10 calls, the code stops for 10
    }

    call_url <- str_glue('https://api.inaturalist.org/v1/taxa/',
                         '{taxon_id_i}')

    get_json_call <- GET(url = call_url) %>%
      content(as = "text") %>% fromJSON(flatten = TRUE)

    results <- as_tibble(get_json_call$results)

    taxaRanks_i <- tibble(taxon_name = results$name,
                          taxon_id = taxon_id_i,
                          taxon_rank = results$rank,
                          observations_count = results$observations_count)

    taxaRanks <- rbind(taxaRanks, taxaRanks_i)
    num_results <- num_results + 1
    cat(num_results, '\n')
  }
  return(taxaRanks)
}
```

I will download all the records on 2023 from here
[naturalista.uy/observations/export](https://www.naturalista.uy/observations/export),
using the following URL query:
quality_grade=any&identifications=any&place_id=7259&verifiable=true&d1=2023-01-01&d2=2023-12-31.
And then read the file.

``` r
observations_2023 <-read_csv('datos/observations-2023-UY.csv')
```

To use the function we will get a unique list of IDs (`taxa_list`).

``` r
taxa_list <- observations_2023 %>%
  filter(!is.na(taxon_id)) %>%
  distinct(taxon_id) %>% pull(taxon_id)
```

We then run the function.

``` r
taxonRank_obs_may_2023 <- getiNatTaxonRank(taxa_list)
```

**This will take a long time**, because each call takes a second and after 10 calls the code sleeps for 10 seconds. So, depending on how many records you have, prepare to wait! Now, let’s reply our questions.

1.  **new for iNaturalist**, i.e., a species recorded in Uruguay in 2023
    that has no previous records in the platform.

To answer this we will assess the field `observations_count`. If the
taxon has only 1 record, then we have a new species!

``` r
taxonRank_observations_2023 %>%
  filter(observations_count==1) %>%
  kable()
```

| taxon_name                          | taxon_id | taxon_rank | observations_count |
|:------------------------------------|---------:|:-----------|-------------------:|
| Helicotropis hookeri                |  1441361 | species    |                  1 |
| Eleocharis contracta                |  1443001 | species    |                  1 |
| Zelurus weyrauchi                   |  1443866 | species    |                  1 |
| Myrceugenia mesomischa              |  1405723 | species    |                  1 |
| Charadrius collaris                 |     4807 | species    |                  1 |
| Teloschistes cymbalifer             |   864266 | species    |                  1 |
| Prosopis nigra                      |   138685 | species    |                  1 |
| Eumusonia livida                    |   750671 | species    |                  1 |
| Mikania sulcata                     |  1004514 | species    |                  1 |
| Pavonia orientalis                  |  1469019 | species    |                  1 |
| Ozotoceros bezoarticus uruguayensis |  1449698 | subspecies |                  1 |
| Anodontites patagonica              |  1143075 | species    |                  1 |

There are 12 species! We could also try if we have any second records.

``` r
taxonRank_observations_2023 %>%
  filter(observations_count==2) %>%
  kable()
```

| taxon_name                | taxon_id | taxon_rank | observations_count |
|:--------------------------|---------:|:-----------|-------------------:|
| Jacquemontia cataractae   |  1440659 | species    |                  2 |
| Tragia pinnata            |  1353278 | species    |                  2 |
| Phytophthora citrophthora |   360245 | species    |                  2 |
| Mycotretus tigrinus       |  1246922 | species    |                  2 |
| Cylindera sinuosa         |  1454925 | species    |                  2 |
| Eurata strigiventris      |   415132 | species    |                  2 |
| Cypella charruana         |  1376035 | species    |                  2 |

There are 7 species that were recorded for the second time in the
platform on 2023.

2.  **new for NaturalistaUY**: i.e., a species recorded in Uruguay in
    2023 that has been recorded before in the platform, but has no
    previous records in Uruguay.

To find out if we have any new records in Uruguay, we cannot use the
`observations_count` because this value is relative to the entire
platform. Thus, we will need to get all the records uploaded in Uruguay
(yes, all the recods!). This is a huge database (more than 90.000
records) but can be downloaded from here
[naturalista.uy/observations/export](https://www.naturalista.uy/observations/export),
using the following URL query:
quality_grade=any&identifications=any&place_id=7259&verifiable=true

``` r
obs_NatUY_before2023 <-read_csv('datos/observations-UY-before2023.csv')
```

Now, we will check all the `taxon_id` (to the species level
`taxon_rank`) to see how many records they have in Uruguay before 2023.

| taxon_name                    | taxon_id | taxon_rank | observations_count |
|:------------------------------|---------:|:-----------|-------------------:|
| Jacquemontia cataractae       |  1440659 | species    |                  2 |
| Phytophthora citrophthora     |   360245 | species    |                  2 |
| Mycotretus tigrinus           |  1246922 | species    |                  2 |
| Cylindera sinuosa             |  1454925 | species    |                  2 |
| Jupunba langsdorffii          |  1455009 | species    |                  3 |
| Agalinis digitalis            |  1444854 | species    |                  3 |
| Epicauta zebra                |  1349935 | species    |                  3 |
| Baccharis megapotamica        |  1197408 | species    |                  3 |
| Serjania herteri              |  1013428 | species    |                  3 |
| Anodontites trapezea          |  1051232 | species    |                  3 |
| Neoconocephalus parvus        |  1468635 | species    |                  3 |
| Pavonia friesii               |  1232616 | species    |                  3 |
| Croton hilarii                |  1448601 | species    |                  3 |
| Scutia arenicola              |  1442712 | species    |                  4 |
| Odontesthes perugiae          |   608799 | species    |                  4 |
| Coccoloba warmingii           |  1468088 | species    |                  5 |
| Pseudomicrothorax agilis      |  1445057 | species    |                  5 |
| Mastophora catarina           |   519650 | species    |                  5 |
| Lippia hieraciifolia          |  1352870 | species    |                  5 |
| Mesembryanthemum theurkauffii |   483713 | species    |                  6 |
| Nyttum beckeri                |  1274613 | species    |                  7 |
| Ludwigia hookeri              |  1169172 | species    |                  7 |
| Croton campestris             |  1296071 | species    |                  7 |
| Pluchea laxiflora             |  1181589 | species    |                  8 |
| Mikania ternata               |  1004523 | species    |                  8 |

In total we have 484 species. Here’s a sample of the 25 species with 2
or more observations on iNaturalist (see `observations_count`).


### That’s all folks!

**WARNING**:
Please bear in mind that this exercise does not handle taxon changes very well,
e.g., *Prosopis nigra* is considered novel when it has actually suffered a taxon
name change.
