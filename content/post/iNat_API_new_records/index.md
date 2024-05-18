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
lastMod: '2024-05-18T00:00:00Z'
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

> **Note**
> I updated this post because I found out an easier way of doing it.
> You can find the previous version [here](https://github.com/bienflorencia/personal-site/blob/6e692db4b693ee00ee1d6138f3d6046c4b3450ac/content/post/iNat_API_new_records/index.md).

This time, I'm interested in detecting if any of the records uploaded to
iNaturalist in Uruguay during the past year, 2023, belong to new species
for NaturalistaUY (our national site) or for the iNaturalist global
platform. To answer this, I once again explored the [iNaturalist
API](https://api.inaturalist.org/v1/docs/). If you want to know more
about my first experiment with the API, see my previous post [How many
users in NaturalistaUY are
Uruguayan?](https://flograttarola.com/post/inat_api/).

So, I want to know if there is an observation that records:

-   **a new species for iNaturalist**, i.e., a species recorded in
    Uruguay in 2023 that has no previous records in the platform.
-   **a new species for NaturalistaUY**: i.e., a species recorded in
    Uruguay in 2023 that has no previous records in Uruguay.

Luckily, the API has a call for
[observations/species_counts](https://api.inaturalist.org/v1/docs/#!/Observations/get_observations_species_counts)
which can take a species or a list of species ids (`taxon_id`) and a
location (`place_id`), and return, among other things, the number of
observations the species has for that place (`count`) and also the
number of observations the species has globally (`observations_count`).

So, let's create the function `getTaxonCount()` which takes a `taxon_id`
(or a list) and a `place_id` as arguments, and returns the `taxon_name`,
`taxon_rank` (e.g., species, genus, or family), `observations_place`
(number of records for the species at the `place_id`), and
`observations_iNat` (number of records for the species globally for
iNat). The `place_id` of Uruguays is `7259.`

``` r
library(httr)
library(jsonlite)
library(knitr)
library(tidyverse)

getTaxonCount <- function(taxon_id, place_id = 7259){

  taxonCount <- tibble(taxon_id = numeric(),
                       taxon_name = character(),
                       taxon_rank = character(),
                       observations_place = numeric(),
                       observations_iNat = numeric())

  num_results = 1 # used to put the API to sleep and print on the console the num

  for (taxon_id_i in taxon_id) {  

    if ((num_results %% 10) + 10 == 10) {
      Sys.sleep(10) # every 10 calls, the code stops for 10
    }

    call_url <- str_glue('https://api.inaturalist.org/v1/observations/species_counts/?',
                         'verifiable=true&',
                         'place_id={place_id}&',
                         'taxon_id={taxon_id_i}')

    get_json_call <- GET(url = call_url) %>%
      content(as = "text") %>% fromJSON(flatten = TRUE)
    results <- as_tibble(get_json_call$results)

    if(get_json_call$total_results == 1) {

      taxonCount_i <- tibble(taxon_id = results$taxon.id,
                             taxon_name = results$taxon.name,
                             taxon_rank = results$taxon.rank,
                             observations_place = results$count,
                             observations_iNat = results$taxon.observations_count)
      taxonCount <- rbind(taxonCount, taxonCount_i)
      cat(num_results-1, 'taxon:', taxonCount_i$taxon_name,
          taxonCount_i$observations_iNat, 'observations on iNat\n')
    } else {
      cat(num_results, taxon_id_i,  'no info\n')
    }
    num_results <- num_results + 1
  }
  return(taxonCount)
}
```

<div>

> **Warning**:
> If the `taxon_id` belongs to a family, order or other higher taxonomic
> ranks, the search may return many taxa (all those listed for that
> `taxon_id`). To avoid this, the function `getTaxonCount()` will not
> return data for such `taxon_id`s.

</div>

To use the function I downloaded all the observations recorded in
Uruguay on 2023 from here:
[naturalista.uy/observations/export](https://www.naturalista.uy/observations/export),
using the following URL query:
`quality_grade=any&identifications=any&place_id=7259&verifiable=true&d1=2023-01-01&d2=2023-12-31`.

Once downloaded the data, I read the file.

``` r
observations_2023 <- read_csv('datos/observations-2023-UY.csv',
                              guess_max = 30000)
```

And then I got a unique list of taxon IDs (`taxa_list`).

``` r
taxa_list <- observations_2023 %>%
  filter(!is.na(taxon_id)) %>%
  distinct(taxon_id) %>% pull(taxon_id)
```

Finally, I run the function with the `taxon_list` I extracted as the
argument of the function `getTaxonCount()`.

``` r
taxon_count_observations_2023 <- getTaxonCount(taxon_id = taxa_list,
                                              place_id = 7259)
```

    1 121850 no info
    1 taxon: Xiruana 279 observations on iNat
    2 taxon: Excirolana armata 16 observations on iNat
    3 taxon: Rapana venosa 807 observations on iNat
    4 taxon: Cyperus trigynus 132 observations on iNat
    5 taxon: Salvator merianae 7033 observations on iNat
    6 taxon: Colaptes campestris 4627 observations on iNat
    7 taxon: Teius oculatus 241 observations on iNat
    8 taxon: Nierembergia aristata 130 observations on iNat
    9 taxon: Ctenucha rubriceps 879 observations on iNat

    # A tibble: 9 × 5
      taxon_id taxon_name            taxon_rank observations_place observations_iNat
         <int> <chr>                 <chr>                   <int>             <int>
    1   419737 Xiruana               genus                      15               279
    2  1028885 Excirolana armata     species                     5                16
    3   370913 Rapana venosa         species                    68               807
    4  1515377 Cyperus trigynus      species                    72               132
    5   318758 Salvator merianae     species                   297              7033
    6    18262 Colaptes campestris   species                   202              4627
    7   113828 Teius oculatus        species                    44               241
    8   961374 Nierembergia aristata species                     9               130
    9   541510 Ctenucha rubriceps    species                    46               879

We will now merge this list with our original data (all the
observations), to get for each `taxon_id` a number of records in the
platform and a number for Uruguay.

``` r
observations_2023 <- left_join(observations_2023,
                               taxon_count_observations_2023,
                               by='taxon_id',
                               relationship='many-to-one')
```

Finally, let's answer our questions.

## 1. **A new species for iNaturalist**

To answer this we will assess the field `observations_iNat` and check
that the `taxon_id`'s `taxon_rank` is 'species', the observation is
'Research Grade' and it has only 1 `observations_iNat`. Those that pass
this check will be new species in Uruguay for the platform!

``` r
observations_2023 %>%
  filter(taxon_rank == 'species' &
           quality_grade == 'research' &
           observations_iNat==1) %>%
  select(iconic_taxon_name,
         taxon_name, observations_iNat) %>%
  arrange(iconic_taxon_name, taxon_name) %>%
  kable()
```

| iconic_taxon_name | taxon_name                | observations_iNat |
|:------------------|:--------------------------|------------------:|
| Insecta           | Chlaenius violatus        |                 1 |
| Insecta           | Eutheria piperata         |                 1 |
| Plantae           | Euphorbia burkartii       |                 1 |
| Plantae           | Grindelia linearifolia    |                 1 |
| Plantae           | Herbertia furcata         |                 1 |
| Plantae           | Mikania sulcata           |                 1 |
| Plantae           | Noticastrum chebataroffii |                 1 |
| Plantae           | Pavonia glutinosa         |                 1 |
| Plantae           | Pavonia orientalis        |                 1 |
| Plantae           | Sisyrinchium rosengurttii |                 1 |
| Plantae           | Vicia montevidensis       |                 1 |

This means, 11 new species were recorded in the platform on 2023!

## 2. **A new species for NaturalistaUY**

In total we have 3209 species in Uruguay for 2023. Here's a sample of
the most recorded species per iconic taxon group.

``` r
observations_2023 %>%
  filter(taxon_rank == 'species') %>%
  group_by(iconic_taxon_name, taxon_name) %>%
  count() %>%
  group_by(iconic_taxon_name) %>%
  filter(n == max(n)) %>%
  kable()
```

| iconic_taxon_name | taxon_name                |   n |
|:------------------|:--------------------------|----:|
| Actinopterygii    | Diplodus argenteus        |  15 |
| Amphibia          | Boana pulchella           |  72 |
| Animalia          | Bunodosoma cangicum       |  42 |
| Arachnida         | Argiope argentata         |  68 |
| Aves              | Furnarius rufus           | 108 |
| Chromista         | Pseudomicrothorax agilis  |   3 |
| Fungi             | Trametes sanguinea        |  31 |
| Insecta           | Harmonia axyridis         |  91 |
| Mammalia          | Hydrochoerus hydrochaeris |  60 |
| Mollusca          | Pachycymbiola brasiliana  |  27 |
| Plantae           | Senecio crassiflorus      | 165 |
| Protozoa          | Fuligo septica            |   2 |
| Protozoa          | Reticularia lycoperdon    |   2 |
| Reptilia          | Salvator merianae         |  69 |
| NA                | Nostoc commune            |   4 |

To know how many species were recorded for the first time in Uruguay, we
assessed the field `observations_place`. If the `taxon_id`'s
`taxon_rank` is 'species', the observation is 'Research Grade' and it
has only 1 `observations_place`, then we have a new species for Uruguay!
And we actually have many 🤩

Here's a sample of new species per iconic taxon group:

``` r
observations_2023 %>%
  filter(taxon_rank == 'species' &
           quality_grade == 'research' &
           observations_place==1) %>%
  group_by(iconic_taxon_name, taxon_name) %>%
  count() %>%
  group_by(iconic_taxon_name) %>%
  slice_head(n=3) %>%
  kable()
```

| iconic_taxon_name | taxon_name                |   n |
|:------------------|:--------------------------|----:|
| Actinopterygii    | Aluterus monoceros        |   1 |
| Actinopterygii    | Austrolebias alexandri    |   1 |
| Actinopterygii    | Austrolebias melanoorus   |   1 |
| Animalia          | Potamotrygon falkneri     |   1 |
| Arachnida         | Acropsopilio chilensis    |   1 |
| Arachnida         | Akela ruricola            |   1 |
| Arachnida         | Creugas lisei             |   1 |
| Aves              | Arundinicola leucocephala |   1 |
| Aves              | Fulmarus glacialis        |   1 |
| Aves              | Fulmarus glacialoides     |   1 |
| Fungi             | Agaricus devoniensis      |   1 |
| Fungi             | Cookeina speciosa         |   1 |
| Fungi             | Hygrocybe flavescens      |   1 |
| Insecta           | Acledra fraterna          |   1 |
| Insecta           | Adimantus ornatissimus    |   1 |
| Insecta           | Anisophya arreguii        |   1 |
| Mammalia          | Chrysocyon brachyurus     |   1 |
| Mollusca          | Pomacea scalaris          |   1 |
| Plantae           | Amaranthus caudatus       |   1 |
| Plantae           | Apium graveolens          |   1 |
| Plantae           | Asplenium inaequilaterale |   1 |
| Protozoa          | Reticularia lycoperdon    |   1 |
| Reptilia          | Caiman yacare             |   1 |
| Reptilia          | Tropidurus torquatus      |   1 |

In total there were 131 new species recorded in iNaturalist for Uruguay
in 2023! Woooow!

And here are the top ten users contributing to these new records:

``` r
observations_2023 %>%
  filter(taxon_rank == 'species' &
           quality_grade == 'research' &
           observations_place==1) %>%
  group_by(user_login) %>%
  count() %>%
  arrange(desc(n)) %>% head(n=10) %>%
  kable()
```

| user_login      |   n |
|:----------------|----:|
| enriquecenoz    |  12 |
| santiagomailhos |  11 |
| amailhos        |   9 |
| luisvescia      |   6 |
| javierpiquillen |   5 |
| gusper          |   4 |
| msilvera        |   4 |
| lautaro_fuentes |   3 |
| m_coronel94     |   3 |
| martzz          |   3 |

Congrats!

## And, that's all !

Hope you find this useful too ✨
