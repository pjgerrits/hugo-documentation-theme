---
title: "GRW-tutorial-CASA"
linktitle: GRW-tutorial-CASA
type: book
author: "PietGerrits"
date: "01/12/2021"
weight: 3
---



## Summary

This page contains a test of GWR functionality I have been experimenting with so far. I have been following a tutorial that was provided by Dr Andrew MacLachlan at UCL CASA and is part of the Geographic Information Systems and Science module.Link to the actual tutorial can be found here:

https://andrewmaclachlan.github.io/CASA0005repo_20192020/gwr-and-spatially-lagged-regression.html

## setup libraries

    library(tidyverse)
    library(tmap)
    library(geojsonio)
    library(plotly)
    library(rgdal)
    library(broom)
    library(mapview)
    library(crosstalk)
    library(sf)
    library(sp)
    library(spdep)
    library(car)
    library(fs)
    library(janitor)



## download a zip file


```r
download.file("https://data.london.gov.uk/download/statistical-gis-boundary-files-london/9ba8c833-6370-4b11-abdc-314aa020d5e0/statistical-gis-boundaries-london.zip",  destfile="statistical-gis-boundaries-london.zip")

#unzip it
unzip("statistical-gis-boundaries-london.zip", exdir="prac9_data")
```

## list directories


```r
list.dirs("prac9_data/statistical-gis-boundaries-london")
```

```
## [1] "prac9_data/statistical-gis-boundaries-london"        
## [2] "prac9_data/statistical-gis-boundaries-london/ESRI"   
## [3] "prac9_data/statistical-gis-boundaries-london/MapInfo"
```




