---
layout:     post
title:      Test Post
date:       2020-07-12
summary:    Choropleths / heatmaps in R's ggplot2
categories: visualization
leaflet:    true
---

Intro

_![final version](/images/nyc-ggplot.png)_


## Table of Contents
{:.no_toc}

* TOC
{:toc}

## The Data
Data?

### Trip Records
{:.no_toc}

The data is made public by TLC under open data, something, provided by the [NYC Taxi & Limousine Commission](https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page). The data is enormous, so I just picked a single month: April 2013.


### GeoJSON
{:.no_toc}



Contains:

{% highlight json %}
{
  "type": "Feature",
  "properties": {
    "shape_area":  "0.0007823067885",
    "objectid":    "1",
    "shape_leng":  "0.116357453189",
    "location_id": "1",
    "zone":        "Newark Airport",
    "borough":     "EWR"
  },
  "geometry": {
    "type":        "MultiPolygon",
    "coordinates": [[[[-74.18, 40.69], ... ]]]
  }
}
{% endhighlight %}

Imported from:

{% highlight R %}
library(sf)

taxi_zones <- read_sf('nyc_taxi_zones.geojson') %>%
  mutate(location_id = as.numeric(location_id))

head(taxi_zones)
{% endhighlight %}


| shape_area        | objectid | shape_leng      | location_id | zone                    | borough       | geometry               |
|:------------------+:---------+:----------------|:------------+:------------------------+:--------------+:-----------------------|
| 0.0007823067885   | 1        | 0.116357453189  | 1           | Newark Airport          | EWR           | <S3: sfc_MULTIPOLYGON> |
| 0.00486634037837  | 2        | 0.43346966679   | 2           | Jamaica Bay             | Queens        | <S3: sfc_MULTIPOLYGON> |
| 0.000314414156821 | 3        | 0.0843411059012 | 3           | Allerton/Pelham&nbsp;Gardens | Bronx         | <S3: sfc_MULTIPOLYGON> |
| 0.000111871946192 | 4        | 0.0435665270921 | 4           | Alphabet City           | Manhattan     | <S3: sfc_MULTIPOLYGON> |
| 0.000497957489363 | 5        | 0.0921464898574 | 5           | Ardern Heights          | Staten&nbsp;Island | <S3:&nbsp;sfc_MULTIPOLYGON> |



## Plot the zones

{% highlight R %}
library(ggplot2)

ggplot(plot_data) +
  geom_sf() +
  coord_sf()
{% endhighlight %}

![plotting polygons](/images/nyc-ggplot-1.png)


## Adding some colour
Count the number of taxi pickups per taxi zone for the month of April 2013 and join the datasets.

{% highlight R %}
taxi_pickups <- read_csv('nyc_taxi_pickups_apr_2013.csv')

plot_data <- taxi_zones %>%
  left_join(taxi_pickups, by = 'location_id')
{% endhighlight %}


{% highlight R %}
ggplot(plot_data) +
  geom_sf(
    mapping = aes(fill = n_pickups),
    colour  = 'black',
    size    = 0.1
  ) +
  coord_sf() +
  scale_fill_viridis(
    option = 'viridis',
    alpha  = 0.7
  )
{% endhighlight %}

![adding some colour](/images/nyc-ggplot-2.png)

## Colour Updates
The colours above are a bit boring - the majority of the plot is hte same colour. Want a different colour palette and log scale.

{% highlight R %}
library(viridis)

ggplot(plot_data) +
  geom_sf(
    mapping = aes(fill = n),
    colour  = 'black', # outline our polygons in black
    size    = 0.1
  ) +
  coord_sf() +
  scale_fill_viridis(
    option = 'viridis',
    trans  = 'log',
    alpha  = 0.7
  )
{% endhighlight %}

![improved colours](/images/nyc-ggplot-3.png)

## Fixing the legend
colours look much more distributed using the log scale, but the legend looks awful. If we give our legend specific breaks and labels in `scale_fill_viridis()`, we can override the default settings.

{% highlight R %}
scale_fill_viridis(
  option = 'viridis',
  name   = 'Number of pickups',
  trans  = 'log',
  breaks = c(10, 100, 1000, 10000, 100000),
  labels = c('10', '100', '1,000', '10,000', '100,000'),
  alpha  = 0.7
)
{% endhighlight %}

## Final Formatting
Make ourselves a custom theme.

{% highlight R %}
theme_map <- function(...) {
  theme_void() +
    theme(
      text            = element_text(family = "Helvetica", color = "#22211d"),
      plot.background = element_rect(fill = "#F5F5F5", color = NA),
      plot.margin     = unit(c(.3, .5, .2, .5), "cm"),
      plot.title      = element_text(size = 18),
      plot.subtitle   = element_text(size = 12),
      ...
    )
}
{% endhighlight %}

_![final version](/images/nyc-ggplot.png)_

Full code [here](github-gist)

## References
{:.no_toc}
- https://rstudio.github.io/leaflet/choropleths.html
- https://timogrossenbacher.ch/2019/04/bivariate-maps-with-ggplot2-and-sf/
- https://medium.com/@linniartan/nyc-taxi-data-analysis-part-1-clean-and-transform-data-in-bigquery-2cb1142c6b8b mapping pickup coordinates to taxi zone names
- https://www.andrewheiss.com/blog/2017/09/27/working-with-r-cairo-graphics-custom-fonts-and-ggplot/#mac
font family should work for mac



