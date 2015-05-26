---
layout: post
date: 2015-05-26
title: code for overlaying polygons and rasters
summary: example code for overlaying poygons on rasters to extract landscape covariates
categories: snippets
---

I've been learning `R`s spatial capacities from scratch, which inevitably results in taking wrong turns. This code is to remind me the route if I have polygons in which I want to extract raster data.

Instead what I have are points. Turns out that instead of all this code, you can just use the `buffer` option in the `extract()` function.

This also assumes that both the polygons and rasters are in the same projection.

```
# this all works, but it's slow. it takes 20 minutes per polygon.
# doing it all together with extract() using the buffer command takes a bit
# more than an hour. keeping this for posterity.

# for each port polygon, find the amount of substrate types in each
library(sp); library(raster); library(rgdal)

# get port polygons
port_polys <- dir("port_polys/")
port_polys <- port_polys[-grep(".dbf", port_polys)]
port_polys <- port_polys[-grep(".shp", port_polys)]
port_polys <- port_polys[-grep(".shx", port_polys)]
port_polys <- gsub(".prj","", port_polys)

# poly
hab_types <- function(poly){
  rasterOptions(maxmemory=5e+08,chunksize=5e+07)
  # for 500mb max memory (up from 100gb by default),
  # and 50mb of chunk size (up from 10mb).

  port_df <- readOGR("port_polys/",poly)
  hab <- raster("coastal_habitat_type1.tif")
  # check to make sure interesect
    if(is.null(intersect(extent(hab), extent(port_df)))) warning("habitat raster has no overlap")
  # crop raster down
    clip1 <- crop(hab, extent(port_df))
  # mask to just polygon
    fr <- rasterize(port_df, clip1)
    clip2 <- mask(clip1, fr)
  # find the area of each habitat type
    ext <- extract(clip2, port_df, method = "simple")
  # make table
    tab <- lapply(ext, table)
    s<-sum(tab[[1]])  #sums the table for percentage calculation
    mat<- as.data.frame(tab) # saves meters squared
    mat$Freq <- mat$Freq/1e6 # convert from m2 to km2
    mat2<- as.data.frame(tab[[1]]/s) #calculates percent
    final<-cbind(port_df@data$Pcid,mat,mat2$Freq) #combines into single dataframe
    write.csv(final, paste0("/tigress/efuller/substrate_types/substrate_results/",poly,".csv"))
  return(final)
}

hab_types(port_polys[1])
```
