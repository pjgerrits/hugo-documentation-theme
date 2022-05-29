---
title: "Edirne_settlement_road_regression"
linktitle: Edirne_settlement_road_regression
type: book
author: "PietGerrits"
date: "01/12/2021"
weight: 1
# Prev/next pager order (if `docs_section_pager` enabled in `params.toml`)
---
## 1. libraries
```{r setup, echo=TRUE}
library(leaflet)
library(tmap)
library(dplyr)
library(nngeo)
library(DBI)  # connect to DB
library (knitr)
library(data.table) # conversion
library(sf) # manipulate spatial data
library(ggplot2)  # plotting
library(tidyverse) # plotting and manipulation
library(grid)      # combining plots
library(gridExtra) # combining plots
library(ggpubr)    # combining plots
library(patchwork) # combining plots
```

## 2. Connect to PostgreSQL Database
```{r setup_db, echo=TRUE}

db <- 'urbanoccupations_db'  #provide the name of your db
host_db <- 'aws-eu-central-1-portal.1.dblayer.com' #i.e. # i.e. 'ec2-54-83-201-96.compute-1.amazonaws.com'  
db_port <- '18368'  # or any other port specified by the DBA
db_u <- 'online_user'  
db_p <- 'Peculiar-Crazy9-Trailing'
con <- dbConnect(RPostgres::Postgres(), dbname = db, host=host_db, port=db_port, user=db_u, password=db_p)
```

## 3a. Access PostgreSQL layer and query dataset for Motorized Transport
```{r motor, echo=TRUE}
file_motor = dbGetQuery(con, "select * from piet_phd_data.DH_1940_Edirne_Transport WHERE network_ty IN ('Ausgebaute Allwetterstrasse', 'Fahrstrasse')")
newGeom = st_as_sfc(structure(as.character(file_motor$geom), class = "WKB"),EWKB=TRUE)
file_motor_geom = st_set_geometry(file_motor, newGeom)
# plot(file_motor_geom$geometry)
map_motor = ggplot(file_motor_geom) + geom_sf() + aes(colour = "red")
map_motor
```

## 3b. Access PostgreSQL layer and query dataset for cart Transport
```{r cart, echo=TRUE}
file_cart = dbGetQuery(con, "select * from piet_phd_data.DH_1940_Edirne_Transport WHERE network_ty IN ('Karawanenweg', 'Saumweg')")
newGeom = st_as_sfc(structure(as.character(file_cart$geom), class = "WKB"),EWKB=TRUE)
file_cart_geom = st_set_geometry(file_cart, newGeom)
map_cart = ggplot(file_cart_geom) + geom_sf() + aes(colour = "red") 
map_cart
```

## 3c. Access PostgreSQL layer and query dataset for Walking paths
```{r walking, echo=TRUE}
file_walk = dbGetQuery(con, "select * from piet_phd_data.DH_1940_Edirne_Transport WHERE network_ty IN ('Fußweg')")
newGeom = st_as_sfc(structure(as.character(file_walk$geom), class = "WKB"),EWKB=TRUE)
file_walk_geom = st_set_geometry(file_walk, newGeom)
map_walk = ggplot(file_walk_geom) + geom_sf() + aes(col = "red") 
map_walk
```

## 6a. Access PostgreSQL layer and query dataset for Settlement layer
```{r settlelments, echo=TRUE}
file_settlements = dbGetQuery(con, "select * from piet_phd_data.nfs_settlements_1940_pop_xy where not geom in ('0101000020E610000000000000000000000000000000000000') AND not id in ('210') ")
newGeom = st_as_sfc(structure(as.character(file_settlements$geom), class = "WKB"),EWKB=TRUE)
file_settlements_geom = st_set_geometry(file_settlements, newGeom)
plot(file_settlements_geom$geometry)
```

## 6b. Visualize first few rows of data as a sample

```{r data, echo=TRUE}


kable(head(file_settlements, 10), caption = "Sample of Dataset")
```

## 9a2 Calculate distance from settlements to the nearest roads per type
```{r Eucl Distance, echo=FALSE,results='hide', message=FALSE,fig.keep='all'}

# calculate distance to nearest road for each road category
result1 <-transpose(as.data.table(st_distance(file_settlements_geom, file_motor_geom)))
result2 <-transpose(as.data.table(st_distance(file_settlements_geom, file_cart_geom)))
result3 <-transpose(as.data.table(st_distance(file_settlements_geom, file_walk_geom)))
# filter the nearest distance of each road category
result1_min <- apply(result1,2,min)
result2_min <- apply(result2,2,min)
result3_min <- apply(result3,2,min)
# visualise distance output as talbe.
result1_min_table <- as.data.table(result1_min)
result2_min_table <- as.data.table(result2_min)
result3_min_table <- as.data.table(result3_min)
#rename near column to for the nearest road in M distance
names(result1_min_table)[names(result1_min_table) == "result1_min"] <- "nearest_road_m"
names(result2_min_table)[names(result2_min_table) == "result2_min"] <- "nearest_road_m"
names(result3_min_table)[names(result3_min_table) == "result3_min"] <- "nearest_road_m"
# calculate row number per table
result1_min_table$row_num <- seq.int(nrow(result1_min_table))
result2_min_table$row_num <- seq.int(nrow(result2_min_table))
result3_min_table$row_num <- seq.int(nrow(result3_min_table))
# visualise result
result1_min_table
result2_min_table
result3_min_table
```

## 9c 
```{r Distribution1}
# join the nearest distance with the settlement layer and plot for result1 (motorways)
file_settlements_geom$row_num <- seq.int(nrow(file_settlements_geom)) 
join_table_distance_l <- inner_join(file_settlements_geom, result1_min_table[ , c("nearest_road_m", "row_num")])
plot(join_table_distance_l$both, join_table_distance_l$nearest_road_m)

# join the nearest distance values with the settlement layer and plot for result2 (cart roads)
file_settlements_geom$row_num <- seq.int(nrow(file_settlements_geom)) 
join_table_distance_2 <- inner_join(file_settlements_geom, result2_min_table[ , c("nearest_road_m", "row_num")])
plot(join_table_distance_2$both, join_table_distance_2$nearest_road_m)

# join the nearest distance values with the settlement layer and plot for result3 (walking paths)
file_settlements_geom$row_num <- seq.int(nrow(file_settlements_geom)) 
join_table_distance_3 <- inner_join(file_settlements_geom, result3_min_table[ , c("nearest_road_m", "row_num")])
plot(join_table_distance_3$both, join_table_distance_3$nearest_road_m)


```

## plot distance vs settlement population
```{r Distribution2a}

# calculate regression for result1 (motorways)
model1 <- lm(both ~ nearest_road_m, data = join_table_distance_l)
summary(model1)
# calculate regression for result2 (cart roads)
model2 <- lm(both ~ nearest_road_m, data = join_table_distance_2)
summary(model2)
# calculate regression for result3 (walking paths)
model3 <- lm(both ~ nearest_road_m, data = join_table_distance_3)
summary(model3)
```



## plot distance vs settlement population2
```{r Distribution2b}

# ggplot(data = join_table_distance_l,aes(x.both, y.nearest_road_m)) +
#   stat_summary(fun.data=mean_cl_normal) + 
#   geom_smooth(method='lm', formula= y~x)

# read dataset
# df = join_table_distance_l
# Create the model
fit1 <- lm(join_table_distance_l$nearest_road_m ~ join_table_distance_l$both, data=join_table_distance_l)
fit2 <- lm(join_table_distance_2$nearest_road_m ~ join_table_distance_2$both, data=join_table_distance_2)
fit3 <- lm(join_table_distance_3$nearest_road_m ~ join_table_distance_3$both, data=join_table_distance_3)

#plot the results with lm line - Motor way
p1 <- ggplot(join_table_distance_l, aes(nearest_road_m, both))+
  geom_point() + stat_smooth(method = "lm", col = "red", se = FALSE) + ggtitle("Nearest Motorway distance") + xlab("road distance (m)") + ylab("Pop. count") + labs(caption = paste("Adj R2 = ",signif(summary(fit1)$adj.r.squared, 5),
                     "Intercept =",signif(fit$coef[[1]],5 ),'\n',
                     " Slope =",signif(fit$coef[[2]], 5),
                     " P =",signif(summary(fit)$coef[2,4], 5)))

#plot the results with lm line - Cart road
p2 <- ggplot(join_table_distance_2, aes(nearest_road_m, both))+
  geom_point() + stat_smooth(method = "lm", col = "red", se = FALSE) + ggtitle("Nearest Cartroad distance") + xlab("road distance (m)") + ylab("Pop. count") + labs(caption = paste("Adj R2 = ",signif(summary(fit2)$adj.r.squared, 5),
                     "Intercept =",signif(fit$coef[[1]],5 ),'\n',
                     " Slope =",signif(fit$coef[[2]], 5),
                     " P =",signif(summary(fit)$coef[2,4], 5)))
#plot the results with lm line - Walking path
p3 <- ggplot(join_table_distance_3, aes(nearest_road_m, both))+
  geom_point() + stat_smooth(method = "lm", col = "red", se = FALSE) + ggtitle("Nearest Walking path distance") + xlab("road distance (m)") + ylab("Pop. count") + labs(caption = paste("Adj R2 = ",signif(summary(fit3)$adj.r.squared, 5),
                     "Intercept =",signif(fit$coef[[1]],5 ),'\n',
                     " Slope =",signif(fit$coef[[2]], 5),
                     " P =",signif(summary(fit)$coef[2,4], 5)))

#create layout and save as pdf 
x <-grid.arrange(p1, p2, p3, nrow = 2, ncol = 2)
ggsave("C:/Users/pietg/Documents/GitHub/hugo-documentation-theme/my_grid2.pdf", x)

```



















##EXTRA

## 8. leaflet map of data

```{r leaflet, echo=TRUE}
## 8a.Calculate symbology column
#Classification of population figures and addition of column "colorpal" for symbolisation
file_settlements_geom$colorpal <- cut(file_settlements_geom$both, c(0,250,500,1000,2500,20000), include.lowest = F, labels = c('250', '500', '1000', '2500', '2500+'))

# Colour coding of the legend and symbols in leaflet map
beatCol <- colorFactor(palette = 'BuPu', file_settlements_geom$colorpal)

# 8b. Map creation
map <- leaflet() %>% 
  addTiles() %>%
  addMiniMap( position = "topleft",   toggleDisplay = TRUE, autoToggleDisplay = TRUE, collapsedWidth = 19, collapsedHeight = 19, minimized=TRUE) %>%
  addProviderTiles(providers$Stamen.Toner, group = 'Toner')  %>%
  addProviderTiles(provider = providers$Esri.WorldImagery, group = 'ESRI')  %>%
  addCircleMarkers(data=file_settlements_geom, file_settlements_geom$newpoint_x, file_settlements_geom$newpoint_y, color = ~beatCol(colorpal), radius = ~sqrt(both/1000), group = 'Settlements',  opacity = 1, weight = 10, popup = ~paste0("<b>", belediye_koy_original, "</b>","<br/>", format(both, big.mark=","))) %>%
  addPolylines(data=file_motor_geom$geometry, color = "Green",   smoothFactor = 1,  opacity = 0.5, weight =5, group = 'Motorways') %>%
  addPolylines(data=file_cart_geom$geometry, color = "Pink",   smoothFactor = 1,  opacity = 0.4, weight =2, group = 'Cart roads') %>%
  addPolylines(data=file_walk_geom$geometry, color = "Red",   smoothFactor = 1,  opacity = 0.5, weight =4, group = 'Walking paths') %>%
  addLegend('bottomright', pal = beatCol, values = file_settlements_geom$colorpal, title = '<b>Settlement size</b>', opacity = 1) %>%
addLayersControl(
    baseGroups = c("Toner (default)", "ESRI"),
    overlayGroups = c("Settlements", "Motorways", "Cart roads", "Walking paths"),
    options = layersControlOptions(collapsed = FALSE)
  )
map
```

## 9 Exploratory Data Analysis (EDA)

## 9a Calculate distance from settlements to the nearest roads per type
```{r Eucl Distance, echo=FALSE,results='hide', message=FALSE,fig.keep='all'}

library(sf) 
result1 <-st_nn(file_settlements_geom, file_motor_geom)
result2 <-st_nn(file_settlements_geom, file_cart_geom)
result3 <-st_nn(file_settlements_geom, file_walk_geom)
# st_nearest_points(file_motor_geom, file_settlements_geom)
# head(result)
l = st_connect(file_settlements_geom, file_motor_geom, ids = result1, progress = FALSE)
m = st_connect(file_settlements_geom, file_cart_geom, ids = result2, progress = FALSE)
n = st_connect(file_settlements_geom, file_walk_geom, ids = result3, progress = FALSE)
plot(l, col = NA)  # For setting the extent
plot(st_geometry(file_settlements_geom), col = "darkgrey", add = TRUE)
plot(st_geometry(file_motor_geom), col = "red", add = TRUE)
plot(l, add = TRUE)
plot(m, col = NA)  # For setting the extent
plot(st_geometry(file_settlements_geom), col = "darkgrey", add = TRUE)
plot(st_geometry(file_cart_geom), col = "red", add = TRUE)
plot(m, add = TRUE)
plot(n, col = NA)  # For setting the extent
plot(st_geometry(file_settlements_geom), col = "darkgrey", add = TRUE)
plot(st_geometry(file_walk_geom), col = "red", add = TRUE)
plot(n, add = TRUE)
```


## plot distance vs settlement population1
```{r Distribution2b}

par(mfrow = c(2, 2))
#plot the results with lm line - Motor way
plot1 <- plot (join_table_distance_l$both ~join_table_distance_l$nearest_road_m, pch = 16, cex = 1.3, col = "blue", main = "Pop Dist vs Dist to nearest motorways", xlab = "Distance(m)", ylab = "Pop. Number")
lm(join_table_distance_l$both ~ join_table_distance_l$nearest_road_m)
abline(lm(join_table_distance_l$both ~ join_table_distance_l$nearest_road_m), col = "red")

#plot the results with lm line - Cart road
plot2 <- plot(join_table_distance_2$both ~join_table_distance_2$nearest_road_m, pch = 16, cex = 1.3, col = "blue", main = "Pop Dist vs Dist to nearest cart roads", xlab = "Distance(m)", ylab = "Pop. Number")
lm(join_table_distance_2$both ~ join_table_distance_l$nearest_road_m)
abline(lm(join_table_distance_2$both ~ join_table_distance_2$nearest_road_m), col = "red")

#plot the results with lm line - Walking path
plot3 <- plot(join_table_distance_l$both ~join_table_distance_3$nearest_road_m, pch = 16, cex = 1.3, col = "blue", main = "Pop Dist vs Dist to nearest walking paths", xlab = "Distance(m)", ylab = "Pop. Number")
lm(join_table_distance_3$both ~ join_table_distance_3$nearest_road_m)
abline(lm(join_table_distance_3$both ~ join_table_distance_3$nearest_road_m), col = "red")
par(mfrow=c(2,2))

# ggarrange(plot1, plot2, plot3 + rremove("x.text"),
#           labels = c("A", "B", "C"),
#           ncol = 2, nrow = 2)

```

