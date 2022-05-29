---
title: "Edirne_settlement_transport_analysis"
linktitle: Edirne_settlement_1940
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
# map_motor
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
map_walk = ggplot(file_walk_geom) + geom_sf() + aes(color = "blue") 
# map_walk
```

## 3d. Access PostgreSQL layer and query dataset for combined wheeled accessible roads
```{r walking, echo=TRUE}
file_all_accessible = dbGetQuery(con, "select * from piet_phd_data.DH_1940_Edirne_Transport WHERE network_ty IN ('Ausgebaute Allwetterstrasse', 'Fahrstrasse', 'Karawanenweg', 'Saumweg')")
newGeom = st_as_sfc(structure(as.character(file_all_accessible$geom), class = "WKB"),EWKB=TRUE)
file_all_accessible_geom = st_set_geometry(file_all_accessible, newGeom)
map_all_accessible = ggplot(file_all_accessible_geom) + geom_sf() + aes(col = "blue")
# map_all_accessible + map_cart

```

## 4a. Access PostgreSQL layer and query dataset for 1935 Settlement layer
```{r settlelments, echo=TRUE}
file_settlements_1935 = dbGetQuery(con, "select * from piet_phd_data.settlements_combined_v4_yearscombined where not adm_village_id in ('267646', '267547', '267482') and year = 1935 AND il_sancak = 'edirne'")
newGeom_1935 = st_as_sfc(structure(as.character(file_settlements_1935$geom), class = "WKB"),EWKB=TRUE)
file_settlements_geom_1935 = st_set_geometry(file_settlements_1935, newGeom_1935)
# plot(file_settlements_geom_1935$geometry)
```

## 4b. Access PostgreSQL layer and query dataset for 1940 Settlement layer
```{r settlelments, echo=TRUE}
file_settlements_1940 = dbGetQuery(con, "select * from piet_phd_data.nfs_settlements_1940_pop_xy where not geom in ('0101000020E610000000000000000000000000000000000000') AND not id in ('210') ")
newGeom_1940 = st_as_sfc(structure(as.character(file_settlements_1940$geom), class = "WKB"),EWKB=TRUE)
file_settlements_geom_1940 = st_set_geometry(file_settlements_1940, newGeom_1940)
# plot(file_settlements_geom_1940$geometry)
```

## 4c. Access PostgreSQL layer and query dataset for 1955 Settlement layer
```{r settlelments, echo=TRUE}
file_settlements_1955 = dbGetQuery(con, "select * from piet_phd_data.settlements_combined_v4_yearscombined where not adm_village_id in ('243952', '223347', '224614', '224665') and year = 1955 AND il_sancak = 'edirne' ")
newGeom_1955 = st_as_sfc(structure(as.character(file_settlements_1955$geom), class = "WKB"),EWKB=TRUE)
file_settlements_geom_1955 = st_set_geometry(file_settlements_1955, newGeom_1955)
# plot(file_settlements_geom_1955$geometry)
```

## 5. Visualize first few rows of data as a sample for all years

```{r data, echo=TRUE}

#visualise table
kable(head(file_settlements_1935, 10), caption = "Sample of Dataset 1935")
kable(head(file_settlements_1940, 10), caption = "Sample of Dataset 1940")
kable(head(file_settlements_1955, 10), caption = "Sample of Dataset 1955")
```

## 6a Calculate distance from settlements 1935 to the nearest roads per type
```{r Eucl Distance, echo=FALSE,results='hide', message=FALSE,fig.keep='all'}
# # calculate distance to nearest road for each road category
result1_1935 <- st_distance(file_settlements_geom_1935, file_motor_geom)
result2_1935 <- st_distance(file_settlements_geom_1935, file_cart_geom)
result3_1935 <- st_distance(file_settlements_geom_1935, file_walk_geom)
result4_1935 <- st_distance(file_settlements_geom_1935, file_all_accessible_geom)

# # filter the nearest distance of each road category
result1_min_1935 <- apply(result1_1935,1, FUN = min)
result2_min_1935 <- apply(result2_1935,1, FUN = min)
result3_min_1935 <- apply(result3_1935,1, FUN = min)
result4_min_1935 <- apply(result4_1935,1, FUN = min)

# visualize distance output as table.
result1_min_table_1935 <- as.data.table(result1_min_1935)
result2_min_table_1935 <- as.data.table(result2_min_1935)
result3_min_table_1935 <- as.data.table(result3_min_1935)
result4_min_table_1935 <- as.data.table(result4_min_1935)

#rename near column to for the nearest road in M distance
names(result1_min_table_1935)[names(result1_min_table_1935) == "result1_min_1935"] <- "nearest_road_m"
names(result2_min_table_1935)[names(result2_min_table_1935) == "result2_min_1935"] <- "nearest_road_m"
names(result3_min_table_1935)[names(result3_min_table_1935) == "result3_min_1935"] <- "nearest_road_m"
names(result4_min_table_1935)[names(result4_min_table_1935) == "result4_min_1935"] <- "nearest_road_m"

# calculate row number per table
result1_min_table_1935$row_num <- seq.int(nrow(result1_min_table_1935))
result2_min_table_1935$row_num <- seq.int(nrow(result2_min_table_1935))
result3_min_table_1935$row_num <- seq.int(nrow(result3_min_table_1935))
result4_min_table_1935$row_num <- seq.int(nrow(result4_min_table_1935))

# visualize result
result1_min_table_1935
result2_min_table_1935
result3_min_table_1935
result4_min_table_1935

```

## 6b Calculate distance from settlements 1940 to the nearest roads per type
```{r Eucl Distance, echo=FALSE,results='hide', message=FALSE,fig.keep='all'}

# # calculate distance to nearest road for each road category
result1_1940 <- st_distance(file_settlements_geom_1940, file_motor_geom)
result2_1940 <- st_distance(file_settlements_geom_1940, file_cart_geom)
result3_1940 <- st_distance(file_settlements_geom_1940, file_walk_geom)
result4_1940 <- st_distance(file_settlements_geom_1940, file_all_accessible_geom)

# # filter the nearest distance of each road category
result1_min_1940 <- apply(result1_1940,1, FUN = min)
result2_min_1940 <- apply(result2_1940,1, FUN = min)
result3_min_1940 <- apply(result3_1940,1, FUN = min)
result4_min_1940 <- apply(result4_1940,1, FUN = min)

# visualise distance output as table.
result1_min_table_1940 <- as.data.table(result1_min_1940)
result2_min_table_1940 <- as.data.table(result2_min_1940)
result3_min_table_1940 <- as.data.table(result3_min_1940)
result4_min_table_1940 <- as.data.table(result4_min_1940)

#rename near column to for the nearest road in M distance
names(result1_min_table_1940)[names(result1_min_table_1940) == "result1_min_1940"] <- "nearest_road_m"
names(result2_min_table_1940)[names(result2_min_table_1940) == "result2_min_1940"] <- "nearest_road_m"
names(result3_min_table_1940)[names(result3_min_table_1940) == "result3_min_1940"] <- "nearest_road_m"
names(result4_min_table_1940)[names(result4_min_table_1940) == "result4_min_1940"] <- "nearest_road_m"

# calculate row number per table
result1_min_table_1940$row_num <- seq.int(nrow(result1_min_table_1940))
result2_min_table_1940$row_num <- seq.int(nrow(result2_min_table_1940))
result3_min_table_1940$row_num <- seq.int(nrow(result3_min_table_1940))
result4_min_table_1940$row_num <- seq.int(nrow(result4_min_table_1940))

# visualise result
result1_min_table_1940
result2_min_table_1940
result3_min_table_1940
result4_min_table_1940



```

## 6c Calculate distance from settlements 1955 to the nearest roads per type
```{r Eucl Distance, echo=FALSE,results='hide', message=FALSE,fig.keep='all'}
# # calculate distance to nearest road for each road category
result1_1955 <- st_distance(file_settlements_geom_1955, file_motor_geom)
result2_1955 <- st_distance(file_settlements_geom_1955, file_cart_geom)
result3_1955 <- st_distance(file_settlements_geom_1955, file_walk_geom)
result4_1955 <- st_distance(file_settlements_geom_1955, file_all_accessible_geom)

# # filter the nearest distance of each road category
result1_min_1955 <- apply(result1_1955,1, FUN = min)
result2_min_1955 <- apply(result2_1955,1, FUN = min)
result3_min_1955 <- apply(result3_1955,1, FUN = min)
result4_min_1955 <- apply(result4_1955,1, FUN = min)

# visualise distance output as table.
result1_min_table_1955 <- as.data.table(result1_min_1955)
result2_min_table_1955 <- as.data.table(result2_min_1955)
result3_min_table_1955 <- as.data.table(result3_min_1955)
result4_min_table_1955 <- as.data.table(result4_min_1955)

#rename near column to for the nearest road in M distance
names(result1_min_table_1955)[names(result1_min_table_1955) == "result1_min_1955"] <- "nearest_road_m"
names(result2_min_table_1955)[names(result2_min_table_1955) == "result2_min_1955"] <- "nearest_road_m"
names(result3_min_table_1955)[names(result3_min_table_1955) == "result3_min_1955"] <- "nearest_road_m"
names(result4_min_table_1955)[names(result4_min_table_1955) == "result4_min_1955"] <- "nearest_road_m"

# calculate row number per table
result1_min_table_1955$row_num <- seq.int(nrow(result1_min_table_1955))
result2_min_table_1955$row_num <- seq.int(nrow(result2_min_table_1955))
result3_min_table_1955$row_num <- seq.int(nrow(result3_min_table_1955))
result4_min_table_1955$row_num <- seq.int(nrow(result4_min_table_1955))

# visualise result
result1_min_table_1955
result2_min_table_1955
result3_min_table_1955
result4_min_table_1955
```

## 7a join data for 1935 table
```{r Distribution1}
# join the nearest distance with the settlement layer and plot for result1 (motorways)
file_settlements_geom_1935$row_num <- seq.int(nrow(file_settlements_geom_1935))
join_table_distance_l_1935 <- inner_join(file_settlements_geom_1935, result1_min_table_1935[ , c("nearest_road_m", "row_num")])
plot(join_table_distance_l_1935$both, join_table_distance_l_1935$nearest_road_m)

# join the nearest distance values with the settlement layer and plot for result2 (cart roads)
file_settlements_geom_1935$row_num <- seq.int(nrow(file_settlements_geom_1935))
join_table_distance_2_1935 <- inner_join(file_settlements_geom_1935, result2_min_table_1935[ , c("nearest_road_m", "row_num")])
plot(join_table_distance_2_1935$both, join_table_distance_2_1935$nearest_road_m)

# join the nearest distance values with the settlement layer and plot for result3 (walking paths)
file_settlements_geom_1935$row_num <- seq.int(nrow(file_settlements_geom_1935))
join_table_distance_3_1935 <- inner_join(file_settlements_geom_1935, result3_min_table_1935[ , c("nearest_road_m", "row_num")])
plot(join_table_distance_3_1935$both, join_table_distance_3_1935$nearest_road_m)

# join the nearest distance values with the settlement layer and plot for result4 (all wheeled accessible)
file_settlements_geom_1935$row_num <- seq.int(nrow(file_settlements_geom_1935))
join_table_distance_4_1935 <- inner_join(file_settlements_geom_1935, result4_min_table_1935[ , c("nearest_road_m", "row_num")])
# plot(join_table_distance_4_1935$both, join_table_distance_4_1935$nearest_road_m)
```

## 7b join data for 1940 table
```{r Distribution1}
# join the nearest distance with the settlement layer and plot for result1 (motorways)
file_settlements_geom_1940$row_num <- seq.int(nrow(file_settlements_geom_1940))
join_table_distance_l_1940 <- inner_join(file_settlements_geom_1940, result1_min_table_1940[ , c("nearest_road_m", "row_num")])
plot(join_table_distance_l_1940$both, join_table_distance_l_1940$nearest_road_m)

# join the nearest distance values with the settlement layer and plot for result2 (cart roads)
file_settlements_geom_1940$row_num <- seq.int(nrow(file_settlements_geom_1940))
join_table_distance_2_1940 <- inner_join(file_settlements_geom_1940, result2_min_table_1940[ , c("nearest_road_m", "row_num")])
plot(join_table_distance_2_1940$both, join_table_distance_2_1940$nearest_road_m)

# join the nearest distance values with the settlement layer and plot for result3 (walking paths)
file_settlements_geom_1940$row_num <- seq.int(nrow(file_settlements_geom_1940))
join_table_distance_3_1940 <- inner_join(file_settlements_geom_1940, result3_min_table_1940[ , c("nearest_road_m", "row_num")])
plot(join_table_distance_3_1940$both, join_table_distance_3_1940$nearest_road_m)

# join the nearest distance values with the settlement layer and plot for result4 (all wheeled accessible)
file_settlements_geom_1940$row_num <- seq.int(nrow(file_settlements_geom_1940))
join_table_distance_4_1940 <- inner_join(file_settlements_geom_1940, result4_min_table_1940[ , c("nearest_road_m", "row_num")])
# plot(join_table_distance_4_1940$both, join_table_distance_4_1940$nearest_road_m)
```

## 7c join data for 1955 table
```{r Distribution1}
# join the nearest distance with the settlement layer and plot for result1 (motorways)
file_settlements_geom_1955$row_num <- seq.int(nrow(file_settlements_geom_1955))
join_table_distance_l_1955 <- inner_join(file_settlements_geom_1955, result1_min_table_1955[ , c("nearest_road_m", "row_num")])
plot(join_table_distance_l_1955$both, join_table_distance_l_1955$nearest_road_m)

# join the nearest distance values with the settlement layer and plot for result2 (cart roads)
file_settlements_geom_1955$row_num <- seq.int(nrow(file_settlements_geom_1955))
join_table_distance_2_1955 <- inner_join(file_settlements_geom_1955, result2_min_table_1955[ , c("nearest_road_m", "row_num")])
plot(join_table_distance_2_1955$both, join_table_distance_2_1955$nearest_road_m)

# join the nearest distance values with the settlement layer and plot for result3 (walking paths)
file_settlements_geom_1955$row_num <- seq.int(nrow(file_settlements_geom_1955))
join_table_distance_3_1955 <- inner_join(file_settlements_geom_1955, result3_min_table_1955[ , c("nearest_road_m", "row_num")])
plot(join_table_distance_3_1955$both, join_table_distance_3_1955$nearest_road_m)

# join the nearest distance values with the settlement layer and plot for result4 (all wheeled accessible)
file_settlements_geom_1955$row_num <- seq.int(nrow(file_settlements_geom_1955))
join_table_distance_4_1955 <- inner_join(file_settlements_geom_1955, result4_min_table_1955[ , c("nearest_road_m", "row_num")])
# plot(join_table_distance_4_1955$both, join_table_distance_4_1955$nearest_road_m)
```

## 8a plot distance vs settlement 1935 population statistics summaries
```{r Distribution2a}

# calculate regression for result1 (motorways)
model1_1935 <- lm(both ~ nearest_road_m, data = join_table_distance_l_1935)
summary(model1_1935)
# calculate regression for result2 (cart roads)
model2_1935 <- lm(both ~ nearest_road_m, data = join_table_distance_2_1935)
summary(model2_1935)
# calculate regression for result3 (walking paths)
model3_1935 <- lm(both ~ nearest_road_m, data = join_table_distance_3_1935)
summary(model3_1935)
# calculate regression for result4 (all wheel accessible roads)
model4_1935 <- lm(both ~ nearest_road_m, data = join_table_distance_4_1935)
summary(model4_1935)

```

## 8b plot distance vs settlement 1940 population statistics summaries
```{r Distribution2a}

# calculate regression for result1 (motorways)
model1_1940 <- lm(both ~ nearest_road_m, data = join_table_distance_l_1940)
summary(model1_1940)
# calculate regression for result2 (cart roads)
model2_1940 <- lm(both ~ nearest_road_m, data = join_table_distance_2_1940)
summary(model2_1940)
# calculate regression for result3 (walking paths)
model3_1940 <- lm(both ~ nearest_road_m, data = join_table_distance_3_1940)
summary(model3_1940)
# calculate regression for result4 (all wheel accessible roads)
model4_1940 <- lm(both ~ nearest_road_m, data = join_table_distance_4_1940)
summary(model4_1940)
```

## 8c plot distance vs settlement 1955 population statistics summaries
```{r Distribution2a}

# calculate regression for result1 (motorways)
model1_1955 <- lm(both ~ nearest_road_m, data = join_table_distance_l_1955)
summary(model1_1955)
# calculate regression for result2 (cart roads)
model2_1955 <- lm(both ~ nearest_road_m, data = join_table_distance_2_1955)
summary(model2_1955)
# calculate regression for result3 (walking paths)
model3_1955 <- lm(both ~ nearest_road_m, data = join_table_distance_3_1955)
summary(model3_1955)
# calculate regression for result4 (all wheel accessible roads)
model4_1955 <- lm(both ~ nearest_road_m, data = join_table_distance_4_1955)
summary(model4_1955)
```


## 9a plot distance vs settlement population 1935 - graph
```{r Distribution2b}

# Create the model
fit1_1935 <- lm(join_table_distance_l_1935$nearest_road_m ~ join_table_distance_l_1935$both, data=join_table_distance_l_1935)
fit2_1935 <- lm(join_table_distance_2_1935$nearest_road_m ~ join_table_distance_2_1935$both, data=join_table_distance_2_1935)
fit3_1935 <- lm(join_table_distance_3_1935$nearest_road_m ~ join_table_distance_3_1935$both, data=join_table_distance_3_1935)
fit4_1935 <- lm(join_table_distance_4_1935$nearest_road_m ~ join_table_distance_4_1935$both, data=join_table_distance_4_1935)

#plot the results with lm line - Motor way
p1_1935 <- ggplot(join_table_distance_l_1935, aes(nearest_road_m, both))+
  geom_point() + stat_smooth(method = "lm", col = "red", se = FALSE) + ggtitle("Nearest Motorway distance") + xlab("road distance (m)") + ylab("Pop. count") + labs(caption = paste("Adj R2 = ",signif(summary(fit1_1935)$adj.r.squared, 5),
                     "Intercept =",signif(fit1_1935$coef[[1]],5 ),'\n',
                     " Slope =",signif(fit1_1935$coef[[2]], 5),
                     " P =",signif(summary(fit1_1935)$coef[2,4], 5)))

#plot the results with lm line - Cart road
p2_1935 <- ggplot(join_table_distance_2_1935, aes(nearest_road_m, both))+
  geom_point() + stat_smooth(method = "lm", col = "red", se = FALSE) + ggtitle("Nearest Cartroad distance") + xlab("road distance (m)") + ylab("Pop. count") + labs(caption = paste("Adj R2 = ",signif(summary(fit2_1935)$adj.r.squared, 5),
                     "Intercept =",signif(fit2_1935$coef[[1]],5 ),'\n',
                     " Slope =",signif(fit2_1935$coef[[2]], 5),
                     " P =",signif(summary(fit2_1935)$coef[2,4], 5)))
#plot the results with lm line - Walking path
p3_1935 <- ggplot(join_table_distance_3_1935, aes(nearest_road_m, both))+
  geom_point() + stat_smooth(method = "lm", col = "red", se = FALSE) + ggtitle("Nearest Walking path distance") + xlab("road distance (m)") + ylab("Pop. count") + labs(caption = paste("Adj R2 = ",signif(summary(fit3_1935)$adj.r.squared, 5),
                     "Intercept =",signif(fit3_1935$coef[[1]],5 ),'\n',
                     " Slope =",signif(fit3_1935$coef[[2]], 5),
                     " P =",signif(summary(fit3_1935)$coef[2,4], 5)))

#plot the results with lm line - Walking path
p3_1935 <- ggplot(join_table_distance_3_1935, aes(nearest_road_m, both))+
  geom_point() + stat_smooth(method = "lm", col = "red", se = FALSE) + ggtitle("Nearest Walking path distance") + xlab("road distance (m)") + ylab("Pop. count") + labs(caption = paste("Adj R2 = ",signif(summary(fit3_1935)$adj.r.squared, 5),
                     "Intercept =",signif(fit3_1935$coef[[1]],5 ),'\n',
                     " Slope =",signif(fit3_1935$coef[[2]], 5),
                     " P =",signif(summary(fit3_1935)$coef[2,4], 5)))

#plot the results with lm line - All wheeled accessible roads
p4_1935 <- ggplot(join_table_distance_4_1935, aes(nearest_road_m, both))+
  geom_point() + stat_smooth(method = "lm", col = "red", se = FALSE) + ggtitle("Nearest Wheeled-vehicle distance") + xlab("road distance (m)") + ylab("Pop. count") + labs(caption = paste("Adj R2 = ",signif(summary(fit4_1935)$adj.r.squared, 5),
                     "Intercept =",signif(fit4_1935$coef[[1]],5 ),'\n',
                     " Slope =",signif(fit4_1935$coef[[2]], 5),
                     " P =",signif(summary(fit4_1935)$coef[2,4], 5)))


#create layout and save as pdf 
x <-grid.arrange(p1_1935, p2_1935, p3_1935, p4_1935, nrow = 2, ncol = 2)
ggsave("C:/Users/pietg/Documents/GitHub/hugo-documentation-theme/my_grid2_1935.pdf", x)

```

## 9b plot distance vs settlement population 1940 - graph
```{r Distribution2b}

# Create the model
fit1_1940 <- lm(join_table_distance_l_1940$nearest_road_m ~ join_table_distance_l_1940$both, data=join_table_distance_l_1940)
fit2_1940 <- lm(join_table_distance_2_1940$nearest_road_m ~ join_table_distance_2_1940$both, data=join_table_distance_2_1940)
fit3_1940 <- lm(join_table_distance_3_1940$nearest_road_m ~ join_table_distance_3_1940$both, data=join_table_distance_3_1940)
fit4_1940 <- lm(join_table_distance_4_1940$nearest_road_m ~ join_table_distance_4_1940$both, data=join_table_distance_4_1940)

#plot the results with lm line - Motor way
p1_1940 <- ggplot(join_table_distance_l_1940, aes(nearest_road_m, both))+
  geom_point() + stat_smooth(method = "lm", col = "red", se = FALSE) + ggtitle("Nearest Motorway distance") + xlab("road distance (m)") + ylab("Pop. count") + labs(caption = paste("Adj R2 = ",signif(summary(fit1_1940)$adj.r.squared, 5),
                     "Intercept =",signif(fit1_1940$coef[[1]],5 ),'\n',
                     " Slope =",signif(fit1_1940$coef[[2]], 5),
                     " P =",signif(summary(fit1_1940)$coef[2,4], 5)))

#plot the results with lm line - Cart road
p2_1940 <- ggplot(join_table_distance_2_1940, aes(nearest_road_m, both))+
  geom_point() + stat_smooth(method = "lm", col = "red", se = FALSE) + ggtitle("Nearest Cartroad distance") + xlab("road distance (m)") + ylab("Pop. count") + labs(caption = paste("Adj R2 = ",signif(summary(fit2_1940)$adj.r.squared, 5),
                     "Intercept =",signif(fit2_1940$coef[[1]],5 ),'\n',
                     " Slope =",signif(fit2_1940$coef[[2]], 5),
                     " P =",signif(summary(fit2_1940)$coef[2,4], 5)))
#plot the results with lm line - Walking path
p3_1940 <- ggplot(join_table_distance_3_1940, aes(nearest_road_m, both))+
  geom_point() + stat_smooth(method = "lm", col = "red", se = FALSE) + ggtitle("Nearest Walking path distance") + xlab("road distance (m)") + ylab("Pop. count") + labs(caption = paste("Adj R2 = ",signif(summary(fit3_1940)$adj.r.squared, 5),
                     "Intercept =",signif(fit3_1940$coef[[1]],5 ),'\n',
                     " Slope =",signif(fit3_1940$coef[[2]], 5),
                     " P =",signif(summary(fit3_1940)$coef[2,4], 5)))

#plot the results with lm line - All wheeled accessible roads
p4_1940 <- ggplot(join_table_distance_4_1940, aes(nearest_road_m, both))+
  geom_point() + stat_smooth(method = "lm", col = "red", se = FALSE) + ggtitle("Nearest Wheeled-vehicle distance") + xlab("road distance (m)") + ylab("Pop. count") + labs(caption = paste("Adj R2 = ",signif(summary(fit4_1940)$adj.r.squared, 5),
                     "Intercept =",signif(fit4_1940$coef[[1]],5 ),'\n',
                     " Slope =",signif(fit4_1940$coef[[2]], 5),
                     " P =",signif(summary(fit4_1940)$coef[2,4], 5)))

#create layout and save as pdf 
x <-grid.arrange(p1_1940, p2_1940, p3_1940, p4_1940, nrow = 2, ncol = 2)
ggsave("C:/Users/pietg/Documents/GitHub/hugo-documentation-theme/my_grid2_1940.pdf", x)

```

## 9c plot distance vs settlement population 1955 - graph
```{r Distribution2b}

# Create the model
fit1_1955 <- lm(join_table_distance_l_1955$nearest_road_m ~ join_table_distance_l_1955$both, data=join_table_distance_l_1955)
fit2_1955 <- lm(join_table_distance_2_1955$nearest_road_m ~ join_table_distance_2_1955$both, data=join_table_distance_2_1955)
fit3_1955 <- lm(join_table_distance_3_1955$nearest_road_m ~ join_table_distance_3_1955$both, data=join_table_distance_3_1955)
fit4_1955 <- lm(join_table_distance_4_1955$nearest_road_m ~ join_table_distance_4_1955$both, data=join_table_distance_4_1955)

#plot the results with lm line - Motor way
p1_1955 <- ggplot(join_table_distance_l_1955, aes(nearest_road_m, both))+
  geom_point() + stat_smooth(method = "lm", col = "red", se = FALSE) + ggtitle("Nearest Motorway distance") + xlab("road distance (m)") + ylab("Pop. count") + labs(caption = paste("Adj R2 = ",signif(summary(fit1_1955)$adj.r.squared, 5),
                     "Intercept =",signif(fit1_1955$coef[[1]],5 ),'\n',
                     " Slope =",signif(fit1_1955$coef[[2]], 5),
                     " P =",signif(summary(fit1_1955)$coef[2,4], 5)))

#plot the results with lm line - Cart road
p2_1955 <- ggplot(join_table_distance_2_1955, aes(nearest_road_m, both))+
  geom_point() + stat_smooth(method = "lm", col = "red", se = FALSE) + ggtitle("Nearest Cartroad distance") + xlab("road distance (m)") + ylab("Pop. count") + labs(caption = paste("Adj R2 = ",signif(summary(fit2_1955)$adj.r.squared, 5),
                     "Intercept =",signif(fit2_1955$coef[[1]],5 ),'\n',
                     " Slope =",signif(fit2_1955$coef[[2]], 5),
                     " P =",signif(summary(fit2_1955)$coef[2,4], 5)))
#plot the results with lm line - Walking path
p3_1955 <- ggplot(join_table_distance_3_1955, aes(nearest_road_m, both))+
  geom_point() + stat_smooth(method = "lm", col = "red", se = FALSE) + ggtitle("Nearest Walking path distance") + xlab("road distance (m)") + ylab("Pop. count") + labs(caption = paste("Adj R2 = ",signif(summary(fit3_1955)$adj.r.squared, 5),
                     "Intercept =",signif(fit3_1955$coef[[1]],5 ),'\n',
                     " Slope =",signif(fit3_1955$coef[[2]], 5),
                     " P =",signif(summary(fit3_1955)$coef[2,4], 5)))

#plot the results with lm line - All wheeled accessible roads
p4_1955 <- ggplot(join_table_distance_4_1955, aes(nearest_road_m, both))+
  geom_point() + stat_smooth(method = "lm", col = "red", se = FALSE) + ggtitle("Nearest Wheeled-vehicle distance") + xlab("road distance (m)") + ylab("Pop. count") + labs(caption = paste("Adj R2 = ",signif(summary(fit4_1955)$adj.r.squared, 5),
                     "Intercept =",signif(fit4_1955$coef[[1]],5 ),'\n',
                     " Slope =",signif(fit4_1955$coef[[2]], 5),
                     " P =",signif(summary(fit4_1955)$coef[2,4], 5)))

#create layout and save as pdf 
x <-grid.arrange(p1_1955, p2_1955, p3_1955, p4_1955, nrow = 2, ncol = 2)
ggsave("C:/Users/pietg/Documents/GitHub/hugo-documentation-theme/my_grid2_1955.pdf", x)

```



## 9a plot GWR road distance vs settlement population 1935 - graph
```{r Distribution2b}
library(spgwr)
table1 <- data.table(id=c(join_table_distance_l_1935$row_num),
                     longitude=c(join_table_distance_l_1935$newpoint_x),
                     latitude=c(join_table_distance_l_1935$newpoint_y),
                     population=c(join_table_distance_l_1935$both),
                     distance=c(join_table_distance_l_1935$nearest_road_m))

table1_sf <- st_as_sf(table1, coords=c("longitude", "latitude"),
                      crs=4326, agr = "constant")
# plot(table1_sf)

# ?

fbw <- gwr.sel(population ~ distance, 
               data = data = table1_sf, 
               coords=cbind( longitude, latitude),
               longlat = TRUE,
               adapt=FALSE, 
               gweight = gwr.Gauss, 
               verbose = FALSE)
# bw = gwr.sel(population ~ distance, data = table1_sf, coords, adapt=T)
gwr.model = gwr(population ~ distance, data = table1_sf, coords, adapt=bw)
gwr.model









# # Create the model
# fit1_1935 <- lm(join_table_distance_l_1935$nearest_road_m ~ join_table_distance_l_1935$both, data=join_table_distance_l_1935)
# fit2_1935 <- lm(join_table_distance_2_1935$nearest_road_m ~ join_table_distance_2_1935$both, data=join_table_distance_2_1935)
# fit3_1935 <- lm(join_table_distance_3_1935$nearest_road_m ~ join_table_distance_3_1935$both, data=join_table_distance_3_1935)
# fit4_1935 <- lm(join_table_distance_4_1935$nearest_road_m ~ join_table_distance_4_1935$both, data=join_table_distance_4_1935)
# 
# #plot the results with lm line - Motor way
# p1_1935 <- ggplot(join_table_distance_l_1935, aes(nearest_road_m, both))+
#   geom_point() + stat_smooth(method = "lm", col = "red", se = FALSE) + ggtitle("Nearest Motorway distance") + xlab("road distance (m)") + ylab("Pop. count") + labs(caption = paste("Adj R2 = ",signif(summary(fit1_1935)$adj.r.squared, 5),
#                      "Intercept =",signif(fit1_1935$coef[[1]],5 ),'\n',
#                      " Slope =",signif(fit1_1935$coef[[2]], 5),
#                      " P =",signif(summary(fit1_1935)$coef[2,4], 5)))
# 
# #plot the results with lm line - Cart road
# p2_1935 <- ggplot(join_table_distance_2_1935, aes(nearest_road_m, both))+
#   geom_point() + stat_smooth(method = "lm", col = "red", se = FALSE) + ggtitle("Nearest Cartroad distance") + xlab("road distance (m)") + ylab("Pop. count") + labs(caption = paste("Adj R2 = ",signif(summary(fit2_1935)$adj.r.squared, 5),
#                      "Intercept =",signif(fit2_1935$coef[[1]],5 ),'\n',
#                      " Slope =",signif(fit2_1935$coef[[2]], 5),
#                      " P =",signif(summary(fit2_1935)$coef[2,4], 5)))
# #plot the results with lm line - Walking path
# p3_1935 <- ggplot(join_table_distance_3_1935, aes(nearest_road_m, both))+
#   geom_point() + stat_smooth(method = "lm", col = "red", se = FALSE) + ggtitle("Nearest Walking path distance") + xlab("road distance (m)") + ylab("Pop. count") + labs(caption = paste("Adj R2 = ",signif(summary(fit3_1935)$adj.r.squared, 5),
#                      "Intercept =",signif(fit3_1935$coef[[1]],5 ),'\n',
#                      " Slope =",signif(fit3_1935$coef[[2]], 5),
#                      " P =",signif(summary(fit3_1935)$coef[2,4], 5)))
# 
# #plot the results with lm line - Walking path
# p3_1935 <- ggplot(join_table_distance_3_1935, aes(nearest_road_m, both))+
#   geom_point() + stat_smooth(method = "lm", col = "red", se = FALSE) + ggtitle("Nearest Walking path distance") + xlab("road distance (m)") + ylab("Pop. count") + labs(caption = paste("Adj R2 = ",signif(summary(fit3_1935)$adj.r.squared, 5),
#                      "Intercept =",signif(fit3_1935$coef[[1]],5 ),'\n',
#                      " Slope =",signif(fit3_1935$coef[[2]], 5),
#                      " P =",signif(summary(fit3_1935)$coef[2,4], 5)))
# 
# #plot the results with lm line - All wheeled accessible roads
# p4_1935 <- ggplot(join_table_distance_4_1935, aes(nearest_road_m, both))+
#   geom_point() + stat_smooth(method = "lm", col = "red", se = FALSE) + ggtitle("Nearest Wheeled-vehicle distance") + xlab("road distance (m)") + ylab("Pop. count") + labs(caption = paste("Adj R2 = ",signif(summary(fit4_1935)$adj.r.squared, 5),
#                      "Intercept =",signif(fit4_1935$coef[[1]],5 ),'\n',
#                      " Slope =",signif(fit4_1935$coef[[2]], 5),
#                      " P =",signif(summary(fit4_1935)$coef[2,4], 5)))
# 
# 
# #create layout and save as pdf 
# x <-grid.arrange(p1_1935, p2_1935, p3_1935, p4_1935, nrow = 2, ncol = 2)
# ggsave("C:/Users/pietg/Documents/GitHub/hugo-documentation-theme/my_grid2_1935.pdf", x)

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

