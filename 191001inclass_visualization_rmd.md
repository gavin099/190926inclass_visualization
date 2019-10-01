191001\_visualization
================
Gavin Ko
10/1/2019

## Create the weather data

``` r
weather_df = 
  rnoaa::meteo_pull_monitors(c("USW00094728", "USC00519397", "USS0023B17S"), #three sites
                      var = c("PRCP", "TMIN", "TMAX"), #humidity, minimumtemp, highesttemp
                      date_min = "2017-01-01",
                      date_max = "2017-12-31") %>%
  mutate(
    name = recode(id, USW00094728 = "CentralPark_NY", 
                      USC00519397 = "Waikiki_HA",
                      USS0023B17S = "Waterhole_WA"),
    tmin = tmin / 10,
    tmax = tmax / 10) %>%
  select(name, id, everything())
```

    ## Registered S3 method overwritten by 'crul':
    ##   method                 from
    ##   as.character.form_file httr

    ## Registered S3 method overwritten by 'hoardr':
    ##   method           from
    ##   print.cache_info httr

    ## file path:          C:\Users\good_\AppData\Local\rnoaa\rnoaa\Cache/ghcnd/USW00094728.dly

    ## file last updated:  2019-09-26 10:26:06

    ## file min/max dates: 1869-01-01 / 2019-09-30

    ## file path:          C:\Users\good_\AppData\Local\rnoaa\rnoaa\Cache/ghcnd/USC00519397.dly

    ## file last updated:  2019-09-26 10:26:22

    ## file min/max dates: 1965-01-01 / 2019-09-30

    ## file path:          C:\Users\good_\AppData\Local\rnoaa\rnoaa\Cache/ghcnd/USS0023B17S.dly

    ## file last updated:  2019-09-26 10:26:28

    ## file min/max dates: 1999-09-01 / 2019-09-30

``` r
weather_df
```

    ## # A tibble: 1,095 x 6
    ##    name           id          date        prcp  tmax  tmin
    ##    <chr>          <chr>       <date>     <dbl> <dbl> <dbl>
    ##  1 CentralPark_NY USW00094728 2017-01-01     0   8.9   4.4
    ##  2 CentralPark_NY USW00094728 2017-01-02    53   5     2.8
    ##  3 CentralPark_NY USW00094728 2017-01-03   147   6.1   3.9
    ##  4 CentralPark_NY USW00094728 2017-01-04     0  11.1   1.1
    ##  5 CentralPark_NY USW00094728 2017-01-05     0   1.1  -2.7
    ##  6 CentralPark_NY USW00094728 2017-01-06    13   0.6  -3.8
    ##  7 CentralPark_NY USW00094728 2017-01-07    81  -3.2  -6.6
    ##  8 CentralPark_NY USW00094728 2017-01-08     0  -3.8  -8.8
    ##  9 CentralPark_NY USW00094728 2017-01-09     0  -4.9  -9.9
    ## 10 CentralPark_NY USW00094728 2017-01-10     0   7.8  -6  
    ## # ... with 1,085 more rows

## Plot a scatter

``` r
weather_df %>% 
  ggplot(aes(x = tmin, y = tmax, color = name)) + 
  geom_point(alpha = .5) +
  labs(
    title = "Temp plot",
    xlab = "Minimum temperature (C)",
    ylab = "Maximum temperature",
    caption = "Data from NOAA via rnoaa pakcage"
  ) +
  scale_x_continuous(
    breaks = c(-15, 0, 20), #adding data points
    labels = c("-15C", "0C", "20C") #refine their respective lables
  ) + 
  scale_y_continuous( #you can also do data transformation in ggplot directly 
    trans = "sqrt"
  )
```

![](191001inclass_visualization_rmd_files/figure-gfm/adding%20lables%20and%20add%20specific%20data%20points-1.png)<!-- -->

``` r
#Alternative of sqrt transforamtion : 
#scale_y_sqrt()
```

## Change the defult colors

Scale can be really useful in all aspects.

``` r
ggplot_base = weather_df %>% 
  ggplot(aes(x = tmin, y = tmax, color = name)) + 
  geom_point(alpha = .5) +
  labs(
    title = "Temp plot",
    xlab = "Minimum temperature (C)",
    ylab = "Maximum temperature",
    caption = "Data from NOAA via rnoaa pakcage"
  ) +
  #scale_color_hue( #adjustment of colors
    #name = "Location", #legend main title
    #h = c(50,255) #a BAD way to put colors inside the points
  viridis::scale_color_viridis(
  name = "Location",
  labels = c("CentralPark","Waikiki","Waterhole"),
  discrete = T
  )
```

Since the original color of ggplot is barely acceptable, we now utilize
viridis packages.

Let’s do more about the legends parameters.

``` r
ggplot_base + 
  theme_minimal() + 
  theme(legend.position = "bottom")
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](191001inclass_visualization_rmd_files/figure-gfm/change%20the%20legends-1.png)<!-- -->

``` r
#Order does matter. Overarching things go first, then the tweeking stuff.
```

Inclass question: how do we check the NA values quickly?

``` r
weather_df %>% 
  filter(is.na(tmin))
```

    ## # A tibble: 15 x 6
    ##    name       id          date        prcp  tmax  tmin
    ##    <chr>      <chr>       <date>     <dbl> <dbl> <dbl>
    ##  1 Waikiki_HA USC00519397 2017-04-17     5  28.3    NA
    ##  2 Waikiki_HA USC00519397 2017-05-09    NA  NA      NA
    ##  3 Waikiki_HA USC00519397 2017-05-26    NA  NA      NA
    ##  4 Waikiki_HA USC00519397 2017-07-19    NA  NA      NA
    ##  5 Waikiki_HA USC00519397 2017-10-07     0  31.1    NA
    ##  6 Waikiki_HA USC00519397 2017-10-09     0  28.9    NA
    ##  7 Waikiki_HA USC00519397 2017-10-10    10  31.7    NA
    ##  8 Waikiki_HA USC00519397 2017-10-12     0  31.1    NA
    ##  9 Waikiki_HA USC00519397 2017-10-13     0  31.1    NA
    ## 10 Waikiki_HA USC00519397 2017-10-16     5  30      NA
    ## 11 Waikiki_HA USC00519397 2017-10-18     0  29.4    NA
    ## 12 Waikiki_HA USC00519397 2017-10-20    13  30.6    NA
    ## 13 Waikiki_HA USC00519397 2017-10-21     0  30      NA
    ## 14 Waikiki_HA USC00519397 2017-10-22     0  30      NA
    ## 15 Waikiki_HA USC00519397 2017-12-22     0  26.7    NA

## more than one dataset

``` r
central_park = 
  weather_df %>% 
  filter(name == "CentralPark_NY")

waikiki = 
  weather_df %>% 
  filter(name == "Waikiki_HA")

waterhole =
  weather_df %>% 
  filter(name == "Waterhole_WA")

ggplot(data = waikiki, aes(x = date, y = tmax, color = name)) + 
  geom_point(aes(size = prcp)) + 
  geom_line(data = central_park)
```

    ## Warning: Removed 3 rows containing missing values (geom_point).

![](191001inclass_visualization_rmd_files/figure-gfm/stacking%20plots-1.png)<!-- -->
(Brief aside about colors)

``` r
waikiki %>% 
  ggplot(aes(x = date, y = tmax)) + 
  geom_point(color = "navy")
```

    ## Warning: Removed 3 rows containing missing values (geom_point).

![](191001inclass_visualization_rmd_files/figure-gfm/specify%20the%20color-1.png)<!-- -->

``` r
#color statement should be in point argument. Don't ask why, it's ggplot.
```

## Panels

``` r
tmax_tmin_p = 
  weather_df %>% 
  ggplot(aes(x = tmax, y = tmin, color = name)) + 
  geom_point(alpha = .5) +
  theme(legend.position = "none")

prcp_dens_p = 
  weather_df %>% 
  filter(prcp > 0) %>% 
  ggplot(aes(x = prcp, fill = name)) + 
  geom_density(alpha = .5) + 
  theme(legend.position = "none")

tmax_date_p = 
  weather_df %>% 
  ggplot(aes(x = date, y = tmax, color = name)) + 
  geom_point(alpha = .5) +
  geom_smooth(se = FALSE) + 
  theme(legend.position = "bottom")

(tmax_tmin_p + prcp_dens_p) / tmax_date_p
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

    ## `geom_smooth()` using method = 'loess' and formula 'y ~ x'

    ## Warning: Removed 3 rows containing non-finite values (stat_smooth).

    ## Warning: Removed 3 rows containing missing values (geom_point).

![](191001inclass_visualization_rmd_files/figure-gfm/unnamed-chunk-1-1.png)<!-- -->

## Data manipulation

``` r
weather_df %>% 
  mutate(
    name = factor(name),
    name = fct_reorder(name, tmax) #reorder the factors
  ) %>% 
  ggplot(aes(x = name, y = tmax, color = name)) +
  geom_boxplot()
```

    ## Warning: Removed 3 rows containing non-finite values (stat_boxplot).

![](191001inclass_visualization_rmd_files/figure-gfm/unnamed-chunk-2-1.png)<!-- -->

## Restructure then plot

``` r
weather_df %>% 
  pivot_longer(
    tmax:tmin,
    names_to = "observation",
    values_to = "temp"
  ) %>% 
  ggplot(aes(x = temp, fill = observation)) + 
  geom_density(alpha = .5) +
  facet_grid(~name) + #create multiple comparison
  viridis::scale_fill_viridis(discrete = TRUE)
```

    ## Warning: Removed 18 rows containing non-finite values (stat_density).

![](191001inclass_visualization_rmd_files/figure-gfm/unnamed-chunk-3-1.png)<!-- -->
