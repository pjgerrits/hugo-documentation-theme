---
title: "Test_page_new"
linktitle: Test_page_new
type: book
author: "PietGerrits"
date: "01/12/2021"
weight: 3
# Prev/next pager order (if `docs_section_pager` enabled in `params.toml`)
---

<script src="/rmarkdown-libs/htmlwidgets/htmlwidgets.js"></script>
<script src="/rmarkdown-libs/jquery/jquery.min.js"></script>
<link href="/rmarkdown-libs/leaflet/leaflet.css" rel="stylesheet" />
<script src="/rmarkdown-libs/leaflet/leaflet.js"></script>
<link href="/rmarkdown-libs/leafletfix/leafletfix.css" rel="stylesheet" />
<script src="/rmarkdown-libs/proj4/proj4.min.js"></script>
<script src="/rmarkdown-libs/Proj4Leaflet/proj4leaflet.js"></script>
<link href="/rmarkdown-libs/rstudio_leaflet/rstudio_leaflet.css" rel="stylesheet" />
<script src="/rmarkdown-libs/leaflet-binding/leaflet.js"></script>

## R Markdown

This is an R Markdown document. Markdown is a simple formatting syntax for authoring HTML, PDF, and MS Word documents. For more details on using R Markdown see <http://rmarkdown.rstudio.com>.

When you click the **Knit** button a document will be generated that includes both content as well as the output of any embedded R code chunks within the document. You can embed an R code chunk like this:

``` r
summary(cars)
```

    ##      speed           dist       
    ##  Min.   : 4.0   Min.   :  2.00  
    ##  1st Qu.:12.0   1st Qu.: 26.00  
    ##  Median :15.0   Median : 36.00  
    ##  Mean   :15.4   Mean   : 42.98  
    ##  3rd Qu.:19.0   3rd Qu.: 56.00  
    ##  Max.   :25.0   Max.   :120.00

## Including Plots

You can also embed plots, for example:

<img src="/docs/chapter1/testpage_new_files/figure-html/pressure-1.png" width="672" />

Note that the `echo = FALSE` parameter was added to the code chunk to prevent printing of the R code that generated the plot.

## Show map in test

example of a leaflet map in Rmarkdown

<div id="htmlwidget-1" style="width:672px;height:480px;" class="leaflet html-widget"></div>
<script type="application/json" data-for="htmlwidget-1">{"x":{"options":{"crs":{"crsClass":"L.CRS.EPSG3857","code":null,"proj4def":null,"projectedBounds":null,"options":{}}},"calls":[{"method":"addTiles","args":["//{s}.tile.openstreetmap.org/{z}/{x}/{y}.png",null,null,{"minZoom":0,"maxZoom":18,"tileSize":256,"subdomains":"abc","errorTileUrl":"","tms":false,"noWrap":false,"zoomOffset":0,"zoomReverse":false,"opacity":1,"zIndex":1,"detectRetina":false,"attribution":"&copy; <a href=\"http://openstreetmap.org\">OpenStreetMap<\/a> contributors, <a href=\"http://creativecommons.org/licenses/by-sa/2.0/\">CC-BY-SA<\/a>"}]}],"setView":[[51,5],6,[]]},"evals":[],"jsHooks":[]}</script>
