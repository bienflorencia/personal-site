+++
title = "IUCN distribution data"
date = 2020-07-15
tags = ["Biodiversidata", "R"]
categories = []
math = false
draft = false
+++


IUCN species distribution maps are widely used to conduct spatial
analyses. However, these data are not always as accurate in some areas
of the globe. Uruguay, in particular, has a severe lack of information
on the distribution of its species. Luckily, last year the [first
comprehensive open-access biodiversity
database](https://doi.org/10.3897/BDJ.7.e36226) in the country was made
available by Biodiversidata. We are going to use this data to check how
well represented are these species in the IUCN database.

### Let's see !

The vertebrates species list includes **664** tetrapod species.

    ## # A tibble: 4 x 2
    ## # Groups:   class [4]
    ##   class        n
    ##   <chr>    <int>
    ## 1 Amphibia    50
    ## 2 Aves       430
    ## 3 Mammalia   116
    ## 4 Reptilia    68

To analyse the data availabe in IUCN for Uruguay we are going to use the
function `rl_occ_country` from the package
[`rredlist`](https://ropensci.org/tutorials/rredlist_tutorial/). This
function enables us to get country occurrence by species name.

#### Function

Let's create a function (`getIUCNSpeciesInUy`) to find if the species
listed for Uruguay are documented as extant in the country according to
IUCN.

    library(rredlist)
    library(taxize)
    library(tidyverse)

    getIUCNSpeciesInUy <- function(data){
      speciesIsinUy <- data.frame(species= character(),
                                  presence=character(),
                                  establishmentMeans=character(),
                                  numCountries= numeric(),
                                  stringsAsFactors = FALSE)
      for (species in data$species){
        isInUy <- rl_occ_country(species)
        if (isInUy$count == 0) {
          speciesIsinUy_species <- data.frame(species= species,
                                              presence= 'NOT FOUND',
                                              establishmentMeans= NA,
                                              numCountries=NA,
                                              stringsAsFactors = F)
          speciesIsinUy <- rbind(speciesIsinUy, speciesIsinUy_species)
        }
        else if ('UY' %in% isInUy$result$code) {
          speciesIsinUy_species <- data.frame(species= species,
                                              presence= isInUy$result$presence[isInUy$result$code=='UY'],
                                              establishmentMeans=isInUy$result$origin[isInUy$result$code=='UY'],
                                              numCountries=isInUy$count,
                                              stringsAsFactors = F)
          speciesIsinUy <- rbind(speciesIsinUy, speciesIsinUy_species)
        }
        else {
          speciesIsinUy_species <- data.frame(species= species,
                                              presence= 'Not recorded in UY',
                                              establishmentMeans= 'Not recorded in UY',
                                              numCountries=isInUy$count,
                                              stringsAsFactors = F)
          speciesIsinUy <- rbind(speciesIsinUy, speciesIsinUy_species)
        }
      }
      return(speciesIsinUy)
    }

The function needs an object (dataframe) with a column named `species`,
which will be uses for the search.

-   If the species is not found within the IUCN database, a **'NOT
    FOUND'** message will be retrieved, and we will need to check if it
    is in IUCN by its synonym scientific name.
-   Else, if the species name is found in IUCN, the function will search
    if it is recorded in Uruguay (code = 'UY').
-   Then, if the species is found in Uruguay the function will retrieve
    the presence status (`presence`), establishment means
    (`establishmentMeans`) and just as a curiosity, the number of
    countries the species is recorded in (`numCountries`).
-   Else, if the species is not found in Uruguay, it will retrieve
    **'Not recorded in UY'** for those columns.

#### Run

Lets run the function with our data:

    IUCNSpeciesInUy <- getIUCNSpeciesInUy(speciesList_Tetrapods)

This function might take a while to run, depending on the size of your
species list :coffee:

#### Explore

So, let's see how it went.

    IUCNSpeciesInUy %>%  
      select(-numCountries) %>% 
      head(n=20)

    ##                           species           presence establishmentMeans
    ## 1          Dendropsophus sanborni             Extant             Native
    ## 2          Elachistocleis bicolor             Extant             Native
    ## 3                 Boana pulchella             Extant             Native
    ## 4          Leptodactylus gracilis             Extant             Native
    ## 5         Leptodactylus latinasus             Extant             Native
    ## 6        Leptodactylus mystacinus             Extant             Native
    ## 7           Leptodactylus latrans             Extant             Native
    ## 8  Melanophryniscus montevidensis             Extant             Native
    ## 9        Odontophrynus americanus             Extant             Native
    ## 10          Odontophrynus maisuma          NOT FOUND               <NA>
    ## 11           Physalaemus gracilis             Extant             Native
    ## 12             Pleurodema bibroni             Extant             Native
    ## 13                 Pseudis minuta             Extant             Native
    ## 14      Pseudopaludicola falcipes             Extant             Native
    ## 15              Rhinella arenarum             Extant             Native
    ## 16             Rhinella dorbignyi             Extant             Native
    ## 17              Scinax granulatus             Extant             Native
    ## 18           Scinax squalirostris             Extant             Native
    ## 19          Argenteohyla siemersi             Extant             Native
    ## 20              Rhinella diptycha Not recorded in UY Not recorded in UY


According to IUCN, 76 species in our databse are not considered extant
in the country. There's a species considered extinct (*Blastocerus
dichotomus*) and one as possibly extinct (*Chrysocyon brachyurus*). But,
what about those **NOT FOUND**? We need to check on their synonym
species.  

To find synonym species, we can use the function `synonyms` from the
package [`taxize`](https://ropensci.org/tutorials/taxize_tutorial/). 
First, filter the species not found for IUCN, then create a new list 
with this species and run the function `getSynonym`. Finally, run the 
function to check for these species again.

    getSynonym <- function(species){
      species_synonym <- synonyms(species, db='itis', rows=1)
      if(is.na(species_synonym) || is_empty(species_synonym[[1]])){
        species_synonym$synonym <- 'NOT FOUND'
      }
      else {
        species_synonym$synonym <- species_synonym[[1]]$syn_name[1]
      }
      return(species_synonym$synonym)
    }

    synonym_IUCNSpeciesInUy <- IUCNSpeciesInUy %>% 
      filter(presence=='NOT FOUND') %>% 
      mutate(speciesSynonym=map_chr(species, getSynonym)) 

Once we have checked if the synonyms are still not found in the IUCN
database - therefore not assessed - we have all the information we need.  

So, let's explore the final results:

<table class="table table-striped table-hover table-condensed table-responsive" style="margin-left: auto; margin-right: auto;">
<thead>
<tr>
<th style="text-align:left;">
Data on IUCN
</th>
<th style="text-align:left;">
Number of Species
</th>
<th style="text-align:left;">
%
</th>
</tr>
</thead>
<tbody>
<tr>
<td style="text-align:left;">
Extant
</td>
<td style="text-align:left;">
559
</td>
<td style="text-align:left;">
84.2
</td>
</tr>
<tr>
<td style="text-align:left;">
Extinct Post-1500
</td>
<td style="text-align:left;">
1
</td>
<td style="text-align:left;">
0.2
</td>
</tr>
<tr>
<td style="text-align:left;">
Not assessed
</td>
<td style="text-align:left;">
22
</td>
<td style="text-align:left;">
3.3
</td>
</tr>
<tr>
<td style="text-align:left;">
Not recorded in UY
</td>
<td style="text-align:left;">
81
</td>
<td style="text-align:left;">
12.2
</td>
</tr>
<tr>
<td style="text-align:left;">
Possibly Extinct
</td>
<td style="text-align:left;">
1
</td>
<td style="text-align:left;">
0.2
</td>
</tr>
</tbody>
</table>
<img src="/img/IUCN_distributionData.png" width="100%" style="display: block; margin: auto;" />


Around 12% of the species recorded in Uruguay are not represented in the
distribution maps of IUCN.

<br>

And, that's all !
-----------------

Take this gaps in mind next time you want to map species using IUCN
species range maps.

