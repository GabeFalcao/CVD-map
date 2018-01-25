---
title: "Death Rate from Heart Disease and Stroke in the USA - CDC data"
author: "Gabriel Falcao Alencar"
date: "January, 2018"
output: 
  html_document: 
    keep_md: yes
---



# Introduction

Heart disease is the leading cause of death in the USA, accounting for approximately 1 in every 4 deaths (@ref). **I will write more about introduction and include some figures in the next few days**.

## Prerequisites

To perform these maps, you will need to have several extra packages. I'm not going into how to install them, but a quick google search should tell you how to. The main libraries that we will need are:

1. [tidyverse](https://www.tidyverse.org/): this is a package created by Hadley Wickham. This "umbrella" package was designed to make data science analysis and visualition faster and easier. The main packages that we will use in this analysis are:
    + [dplyr](http://dplyr.tidyverse.org/): grammar of data manipulation, i.e. how to import, select, filter, and arrange your data for analysis;
    + [ggplot2](http://ggplot2.tidyverse.org/): system for declaratively creating graphics, i.e. you provide the data, tell it how to map, and it takes care of details;
    + [stringr](http://stringr.tidyverse.org/): provides a set of functions designed to make working with strings as easy as possible.
2. [maps](https://cran.r-project.org/web/packages/maps/README.html): package for displaying maps;
3. [mapdata](https://www.rdocumentation.org/packages/mapdata/versions/2.2-6): supplements the `maps` package, providing some larger and/or higher-resolution databases.
4. [ggmap](https://github.com/dkahle/ggmap): package to retrieve [raster map tiles](https://en.wikipedia.org/wiki/Tiled_web_map) (map displayed by joining dozens of individually requested image files over the internet) from popular online mapping service like Google Maps, and others.

## Load the libraries

```r
library(ggplot2)
library(ggmap)
```

```
## Google Maps API Terms of Service: http://developers.google.com/maps/terms.
```

```
## Please cite ggmap if you use it: see citation("ggmap") for details.
```

```r
library(maps)
library(mapdata)
library(dplyr)
```

```
## 
## Attaching package: 'dplyr'
```

```
## The following objects are masked from 'package:stats':
## 
##     filter, lag
```

```
## The following objects are masked from 'package:base':
## 
##     intersect, setdiff, setequal, union
```

```r
library(stringr)
```

## Plotting the maps

Although the `maps` package is able to plot, we will use `ggplot2` to actually plot. For that, we will need to do some transformations in the data since `ggplot2` operates on data frames.


```r
usa <- map_data("usa")
dim(usa)
```

```
## [1] 7243    6
```

```r
head(usa)
```

```
##        long      lat group order region subregion
## 1 -101.4078 29.74224     1     1   main      <NA>
## 2 -101.3906 29.74224     1     2   main      <NA>
## 3 -101.3620 29.65056     1     3   main      <NA>
## 4 -101.3505 29.63911     1     4   main      <NA>
## 5 -101.3219 29.63338     1     5   main      <NA>
## 6 -101.3047 29.64484     1     6   main      <NA>
```

```r
tail(usa)
```

```
##           long      lat group order         region subregion
## 7247 -122.6187 48.37482    10  7247 whidbey island      <NA>
## 7248 -122.6359 48.35764    10  7248 whidbey island      <NA>
## 7249 -122.6703 48.31180    10  7249 whidbey island      <NA>
## 7250 -122.7218 48.23732    10  7250 whidbey island      <NA>
## 7251 -122.7104 48.21440    10  7251 whidbey island      <NA>
## 7252 -122.6703 48.17429    10  7252 whidbey island      <NA>
```

```r
# Just as an exercise will plot the USA map to define one parameter
ggplot() + geom_polygon(data=usa, aes(x=long, y=lat, group=group)) +
  coord_fixed(1.3)
```

![](cvd-map-markdown_files/figure-html/plot-map-1.png)<!-- -->

That turned out pretty nice. The `coord_fixed()` function is very important when drawing maps. It "forces" a specific ration between the x and y axis. In our example, I asked for each *y* unit to be 1.3*x* unit. Why this is important? Because if we decide to change the size of file that we are saving, it knows the ratios and it will change accordingly, instead of potentially altering the ratios that we will use.

## Plot the county map

Now that we have a few things defined, lets plot the map with the counties just to see how it looks.


```r
counties <- map_data("county")
dim(counties)
```

```
## [1] 87949     6
```

```r
head(counties)
```

```
##        long      lat group order  region subregion
## 1 -86.50517 32.34920     1     1 alabama   autauga
## 2 -86.53382 32.35493     1     2 alabama   autauga
## 3 -86.54527 32.36639     1     3 alabama   autauga
## 4 -86.55673 32.37785     1     4 alabama   autauga
## 5 -86.57966 32.38357     1     5 alabama   autauga
## 6 -86.59111 32.37785     1     6 alabama   autauga
```

```r
tail(counties)
```

```
##            long      lat group order   region  subregion
## 91028 -104.9659 40.00964  3085 91028 colorado broomfield
## 91029 -104.9659 40.00964  3085 91029 colorado broomfield
## 91030 -104.9831 40.00964  3085 91030 colorado broomfield
## 91031 -105.0575 39.91224  3085 91031 colorado broomfield
## 91032 -105.1492 39.90078  3085 91032 colorado broomfield
## 91033 -105.1721 39.91224  3085 91033 colorado broomfield
```

```r
# Just as an exercise will plot the USA map to define one parameter
ggplot() + geom_polygon(data=counties, aes(x=long, y=lat, fill=region, group=group), color="white") +
  coord_fixed(1.3) +
  guides(fill=FALSE)
```

![](cvd-map-markdown_files/figure-html/county-map-1.png)<!-- -->

Now, we have the basic shape that we will use to create our maps.

