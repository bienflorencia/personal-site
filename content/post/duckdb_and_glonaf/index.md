---
title: DuckDB and GloNAF
subtitle: How to process large database files
summary: How to process large database files
authors:
  - admin
tags:
  - R
  - DuckDB
  - GloNAF

categories: []
projects: []
# Date published
date: '2024-02-15T00:00:00Z'
# Date updated
lastMod: '2024-02-15T00:00:00Z'
# Featured image
# Place an image named `featured.jpg/png` in this page's folder and customize its options here.
image:
  caption: ''
  focal_point: ''
  placement: 2
  preview_only: false
---

On my quest to process some **VERY** large files I came across
[DuckDB](https://duckdb.org), an its R package
[`duckdb`](https://duckdb.org/docs/api/r). DuckDB is a relational
(table-oriented) DBMS (database management system) that supports the
Structured Query Language (SQL).

To try it out, as an example, I chose the **GloNAF** ([Global
Naturalized Alien Flora](https://glonaf.org)) database. This database
about naturalised alien plant species, covers all terrestrial regions of
the globe, and includes four tables (*Taxon_x\_List*, *List*, *Region*,
*Reference*) that are linked by different id variables. See [Pyšek et
al. (2017)](https://doi.org/10.23855/preslia.2017.203) for more info.

Since I haven’t seen any code to get data out of the **GloNAF**
database, I’m sharing here the code hoping it helps someone else. I will
use `duckdb` to load the data and perform some queries combining the
different tables.

### Let’s go!

As the [R DuckDB documentation](https://duckdb.org/docs/api/r) explains,
to use DuckDB, you must first create a connection object that represents
the database. We will use `dbConnect()` to start an in-memory database.

``` r
library(duckdb)
library(tmap)
tmap_mode('view')
library(rnaturalearth)
library(sf)
sf::sf_use_s2(FALSE)
library(tidyverse)

duck_glonaf <- dbConnect(duckdb())
```

We will then read the tables to the database using `duckdb_register()`.
If you check your R Memory you will notice that the usage is little and
the queries are super fast!

``` r
duckdb_register(duck_glonaf, 'list',
                read_csv('data/List_GloNAF_vanKleunenetal2018Ecology.csv'))

duckdb_register(duck_glonaf, 'region',
                read_csv('data/Region_GloNAF_vanKleunenetal2018Ecology.csv'))

duckdb_register(duck_glonaf, 'taxon',
                read_csv('data/Taxon_x_List_GloNAF_vanKleunenetal2018Ecology.csv'))
```

Now, we will query the database using `dbGetQuery()`. **DuckDB**
supports the standard database interface methods to send queries and
retrieve result sets. And, *amazingly*, we can also use `dplyr`
functions to process the query.

Our query seeks to get a list of species per country. So, we will need
to combine the tables *Taxon_x_List* and *Region*, using the variable
`region_id`.

``` r
query <-
  'SELECT country_ISO, country, standardized_name
   FROM taxon JOIN region
   ON taxon.region_id = region.region_id'

dbGetQuery(duck_glonaf, query) %>%
  as_tibble() %>%
  distinct(country_ISO, standardized_name, .keep_all = T) -> GLONAF
```

To test our query results, let’s see a sample of 20 taxa from
**Uruguay** \<3 and **Argentina**.

``` r
GLONAF %>% filter(country=='Uruguay' | country=='Argentina') %>%
  select(Country=country, Taxa=standardized_name) %>%
  slice_sample(n = 20)
```

    # A tibble: 20 × 2
       Country   Taxa                           
       <chr>     <chr>                             
     1 Argentina Phyllanthus tenellus              
     2 Uruguay   Rostraria cristata                
     3 Uruguay   Galium aparine                    
     4 Argentina Aira elegantissima                
     5 Argentina Vulpia bromoides                  
     6 Uruguay   Ipomoea cairica                   
     7 Uruguay   Fumaria capreolata                
     8 Argentina Eriochloa pseudoacrotricha        
     9 Argentina Borago officinalis                
    10 Uruguay   Polypogon viridis                 
    11 Argentina Lupinus arboreus                  
    12 Uruguay   Melilotus indicus                 
    13 Argentina Cuscuta epithymum var. macranthera
    14 Argentina Myosotis azorica                  
    15 Uruguay   Fraxinus americana                
    16 Uruguay   Pterocarya rehderiana             
    17 Argentina Barbarea intermedia               
    18 Uruguay   Hedypnois rhagadioloides          
    19 Argentina Lamarckia aurea                   
    20 Argentina Ranunculus muricatus              

And, to finish, let’s map the number of taxa per country. To do that,
we will join the data to a world map using `ne_countries()` from the
`rnaturalearth` package, and finally map the number of taxa per country
with `tmap`.

``` r
world <- ne_countries(scale = 50, returnclass = 'sf') %>%
  select(iso_a3)

GLONAF_taxa <- left_join(world %>% rename(country_ISO=iso_a3),
                         GLONAF) %>%
    group_by(country_ISO) %>%
    summarise(taxa=n_distinct(standardized_name, na.rm=T))

tm_shape(GLONAF_taxa %>% mutate(taxa=ifelse(taxa==0, NA, taxa)),
         bbox = st_bbox(world)) +
  tm_fill('taxa', palette = 'YlGnBu',
          style = 'log10_pretty',
          textNA = '0',
          colorNA='grey95',
          legend.reverse = T,
          title = 'GloNAF taxa count',
          alpha=0.5) +
  tm_view(set.view = 2)
```

<iframe src='tmap.html' height='600' width="100%">
</iframe>

To finish up, we disconnect the database and *voilà*.

``` r
dbDisconnect(duck_glonaf)
```

### That’s all!
