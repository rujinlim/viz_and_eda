Viz part 1
================

``` r
library(tidyverse)
```

    ## ── Attaching core tidyverse packages ──────────────────────── tidyverse 2.0.0 ──
    ## ✔ dplyr     1.1.3     ✔ readr     2.1.4
    ## ✔ forcats   1.0.0     ✔ stringr   1.5.0
    ## ✔ ggplot2   3.4.3     ✔ tibble    3.2.1
    ## ✔ lubridate 1.9.2     ✔ tidyr     1.3.0
    ## ✔ purrr     1.0.2     
    ## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
    ## ✖ dplyr::filter() masks stats::filter()
    ## ✖ dplyr::lag()    masks stats::lag()
    ## ℹ Use the conflicted package (<http://conflicted.r-lib.org/>) to force all conflicts to become errors

``` r
library(ggridges)

# knitr::opts_chunk$set(fig.width = 6, fig.asp = 0.6, fig.height = 0.3)
```

Get the data for plotting today.

``` r
weather_df = 
  rnoaa::meteo_pull_monitors(
    c("USW00094728", "USW00022534", "USS0023B17S"),
    var = c("PRCP", "TMIN", "TMAX"), 
    date_min = "2021-01-01",
    date_max = "2022-12-31") |>
  mutate(
    name = recode(
      id, 
      USW00094728 = "CentralPark_NY", 
      USW00022534 = "Molokai_HI",
      USS0023B17S = "Waterhole_WA"),
    tmin = tmin / 10,
    tmax = tmax / 10) |>
  select(name, id, everything())
```

    ## using cached file: /Users/rujinlim/Library/Caches/org.R-project.R/R/rnoaa/noaa_ghcnd/USW00094728.dly

    ## date created (size, mb): 2023-09-28 10:20:02.328587 (8.524)

    ## file min/max dates: 1869-01-01 / 2023-09-30

    ## using cached file: /Users/rujinlim/Library/Caches/org.R-project.R/R/rnoaa/noaa_ghcnd/USW00022534.dly

    ## date created (size, mb): 2023-09-28 10:20:09.575758 (3.83)

    ## file min/max dates: 1949-10-01 / 2023-09-30

    ## using cached file: /Users/rujinlim/Library/Caches/org.R-project.R/R/rnoaa/noaa_ghcnd/USS0023B17S.dly

    ## date created (size, mb): 2023-09-28 10:20:12.2714 (0.994)

    ## file min/max dates: 1999-09-01 / 2023-09-30

Make plots!

``` r
ggplot(weather_df, aes(x = tmin, y = tmax)) +
  geom_point()
```

    ## Warning: Removed 17 rows containing missing values (`geom_point()`).

![](viz_part1_files/figure-gfm/unnamed-chunk-3-1.png)<!-- -->

Pipes and stuff

``` r
weather_df |> 
  filter(name == "CentralPark_NY") |> 
  ggplot(aes(x=tmin, y=tmax)) +
  geom_point()
```

![](viz_part1_files/figure-gfm/unnamed-chunk-4-1.png)<!-- -->

``` r
ggp_nyc_weather <- 
  weather_df |> 
  filter(name == "CentralPark_NY") |> 
  ggplot(aes(x=tmin, y=tmax)) +
  geom_point()

ggp_nyc_weather
```

![](viz_part1_files/figure-gfm/unnamed-chunk-4-2.png)<!-- -->

## Fancy plot

``` r
ggplot(weather_df, aes(x = tmin, y = tmax, color=name)) +
  geom_point() + 
  geom_smooth() # creates a smooth curves for each variable
```

    ## `geom_smooth()` using method = 'loess' and formula = 'y ~ x'

    ## Warning: Removed 17 rows containing non-finite values (`stat_smooth()`).

    ## Warning: Removed 17 rows containing missing values (`geom_point()`).

![](viz_part1_files/figure-gfm/unnamed-chunk-5-1.png)<!-- -->

``` r
ggplot(weather_df, aes(x = tmin, y = tmax)) +
  geom_point(aes(color=name)) + 
  geom_smooth() # creates a smooth curve for all data
```

    ## `geom_smooth()` using method = 'gam' and formula = 'y ~ s(x, bs = "cs")'

    ## Warning: Removed 17 rows containing non-finite values (`stat_smooth()`).
    ## Removed 17 rows containing missing values (`geom_point()`).

![](viz_part1_files/figure-gfm/unnamed-chunk-5-2.png)<!-- -->

``` r
# never plot curve without data points (geom_smooth without geom_point)

ggplot(weather_df, aes(x = tmin, y = tmax)) +
  geom_point(aes(color=name)) + 
  geom_smooth(se = FALSE) # hides standard error bracket
```

    ## `geom_smooth()` using method = 'gam' and formula = 'y ~ s(x, bs = "cs")'

    ## Warning: Removed 17 rows containing non-finite values (`stat_smooth()`).
    ## Removed 17 rows containing missing values (`geom_point()`).

![](viz_part1_files/figure-gfm/unnamed-chunk-5-3.png)<!-- -->

``` r
ggplot(weather_df, aes(x = tmin, y = tmax)) +
  geom_point(aes(color=name), alpha = 0.3) +  # makes it 30% opaque
  geom_smooth(se = FALSE) 
```

    ## `geom_smooth()` using method = 'gam' and formula = 'y ~ s(x, bs = "cs")'

    ## Warning: Removed 17 rows containing non-finite values (`stat_smooth()`).
    ## Removed 17 rows containing missing values (`geom_point()`).

![](viz_part1_files/figure-gfm/unnamed-chunk-5-4.png)<!-- -->

Plot with facets: previous plot is still difficult to see, now split
into facets for each location

``` r
ggplot(weather_df, aes(x = tmin, y = tmax, color=name)) +
  geom_point(alpha=0.3) + 
  geom_smooth() +
  facet_grid(. ~ name)
```

    ## `geom_smooth()` using method = 'loess' and formula = 'y ~ x'

    ## Warning: Removed 17 rows containing non-finite values (`stat_smooth()`).

    ## Warning: Removed 17 rows containing missing values (`geom_point()`).

![](viz_part1_files/figure-gfm/unnamed-chunk-6-1.png)<!-- -->

``` r
ggplot(weather_df, aes(x = tmin, y = tmax, color=name)) +
  geom_point(alpha=0.3) + 
  geom_smooth() +
  facet_grid(name ~ .)
```

    ## `geom_smooth()` using method = 'loess' and formula = 'y ~ x'

    ## Warning: Removed 17 rows containing non-finite values (`stat_smooth()`).
    ## Removed 17 rows containing missing values (`geom_point()`).

![](viz_part1_files/figure-gfm/unnamed-chunk-6-2.png)<!-- -->

Let’s try a different plot - temps are boring

``` r
ggplot(weather_df, aes(x = date, y = tmax, color = name)) +
  geom_point() +
  geom_smooth()
```

    ## `geom_smooth()` using method = 'loess' and formula = 'y ~ x'

    ## Warning: Removed 17 rows containing non-finite values (`stat_smooth()`).

    ## Warning: Removed 17 rows containing missing values (`geom_point()`).

![](viz_part1_files/figure-gfm/unnamed-chunk-7-1.png)<!-- -->

``` r
ggplot(weather_df, aes(x = date, y = tmax, color = name)) +
  geom_point(alpha = 0.3) +
  geom_smooth()
```

    ## `geom_smooth()` using method = 'loess' and formula = 'y ~ x'

    ## Warning: Removed 17 rows containing non-finite values (`stat_smooth()`).
    ## Removed 17 rows containing missing values (`geom_point()`).

![](viz_part1_files/figure-gfm/unnamed-chunk-7-2.png)<!-- -->

``` r
ggplot(weather_df, aes(x = date, y = tmax, color = name)) +
  geom_point(aes(size = prcp), alpha = 0.3) + # add precipitation points (size)
  geom_smooth()
```

    ## `geom_smooth()` using method = 'loess' and formula = 'y ~ x'

    ## Warning: Removed 17 rows containing non-finite values (`stat_smooth()`).

    ## Warning: Removed 19 rows containing missing values (`geom_point()`).

![](viz_part1_files/figure-gfm/unnamed-chunk-7-3.png)<!-- -->

``` r
ggplot(weather_df, aes(x = date, y = tmax, color = name)) +
  geom_point(aes(size = prcp), alpha = 0.3) +
  geom_smooth() +
  facet_grid(. ~ name)
```

    ## `geom_smooth()` using method = 'loess' and formula = 'y ~ x'

    ## Warning: Removed 17 rows containing non-finite values (`stat_smooth()`).
    ## Removed 19 rows containing missing values (`geom_point()`).

![](viz_part1_files/figure-gfm/unnamed-chunk-7-4.png)<!-- -->

Assign specific colors

``` r
weather_df |> 
  filter(name == "CentralPark_NY") |> 
  ggplot(aes(x=date, y=tmax)) +
  geom_point(color = "blue") # careful not to put this in aes()
```

![](viz_part1_files/figure-gfm/unnamed-chunk-8-1.png)<!-- -->

``` r
weather_df |> 
  filter(name != "CentralPark_NY") |> 
  ggplot(aes(x = date, y = tmax, color = name)) +
  geom_point()
```

    ## Warning: Removed 17 rows containing missing values (`geom_point()`).

![](viz_part1_files/figure-gfm/unnamed-chunk-8-2.png)<!-- -->

``` r
weather_df |> 
  filter(name != "CentralPark_NY") |> 
  ggplot(aes(x = date, y = tmax, color = name)) +
  geom_point(alpha = 0.7, size = 0.5)
```

    ## Warning: Removed 17 rows containing missing values (`geom_point()`).

![](viz_part1_files/figure-gfm/unnamed-chunk-8-3.png)<!-- -->

``` r
weather_df |>  
  ggplot(aes(x = date, y = tmax)) +
  geom_hex()
```

    ## Warning: Removed 17 rows containing non-finite values (`stat_binhex()`).

![](viz_part1_files/figure-gfm/unnamed-chunk-9-1.png)<!-- --> \##
Univariate plotting

Histogram

``` r
ggplot(weather_df, aes(x = tmax)) +
  geom_histogram()
```

    ## `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.

    ## Warning: Removed 17 rows containing non-finite values (`stat_bin()`).

![](viz_part1_files/figure-gfm/unnamed-chunk-10-1.png)<!-- -->

``` r
ggplot(weather_df, aes(x = tmax, fill = name)) + # we observe stacking
  geom_histogram()
```

    ## `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.

    ## Warning: Removed 17 rows containing non-finite values (`stat_bin()`).

![](viz_part1_files/figure-gfm/unnamed-chunk-10-2.png)<!-- -->

``` r
# one way to deal with stacking
ggplot(weather_df, aes(x = tmax, fill = name)) +
  geom_histogram(position = "dodge")
```

    ## `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.

    ## Warning: Removed 17 rows containing non-finite values (`stat_bin()`).

![](viz_part1_files/figure-gfm/unnamed-chunk-10-3.png)<!-- -->

``` r
# Let's use a density plot
ggplot(weather_df, aes(x = tmax, fill = name)) +
  geom_density()
```

    ## Warning: Removed 17 rows containing non-finite values (`stat_density()`).

![](viz_part1_files/figure-gfm/unnamed-chunk-10-4.png)<!-- -->

``` r
ggplot(weather_df, aes(x = tmax, fill = name)) +
  geom_density(alpha = 0.3) # to see clearer
```

    ## Warning: Removed 17 rows containing non-finite values (`stat_density()`).

![](viz_part1_files/figure-gfm/unnamed-chunk-10-5.png)<!-- -->

``` r
ggplot(weather_df, aes(x = tmax, fill = name)) +
  geom_density(alpha = 0.3, adjust = 2) # adjust to change how smooth curves are
```

    ## Warning: Removed 17 rows containing non-finite values (`stat_density()`).

![](viz_part1_files/figure-gfm/unnamed-chunk-10-6.png)<!-- -->

Using boxplots

``` r
ggplot(weather_df, aes(y = tmax, x = name)) +
  geom_boxplot()
```

    ## Warning: Removed 17 rows containing non-finite values (`stat_boxplot()`).

![](viz_part1_files/figure-gfm/unnamed-chunk-11-1.png)<!-- -->

Violin plots

``` r
ggplot(weather_df, aes(y = tmax, x = name)) +
  geom_violin()
```

    ## Warning: Removed 17 rows containing non-finite values (`stat_ydensity()`).

![](viz_part1_files/figure-gfm/unnamed-chunk-12-1.png)<!-- -->

Ridge plot

``` r
ggplot(weather_df, aes(x = tmax, y = name)) +
  geom_density_ridges()
```

    ## Picking joint bandwidth of 1.54

    ## Warning: Removed 17 rows containing non-finite values
    ## (`stat_density_ridges()`).

![](viz_part1_files/figure-gfm/unnamed-chunk-13-1.png)<!-- -->

Line plot

``` r
weather_df |> 
  filter(name == "Molokai_HI") |> 
  ggplot(aes(x = date, y = tmax)) +
  geom_line() 
```

![](viz_part1_files/figure-gfm/unnamed-chunk-14-1.png)<!-- -->

``` r
weather_df |> 
  filter(name == "Molokai_HI") |> 
  ggplot(aes(x = date, y = tmax)) +
  geom_line(alpha = 0.5) + 
  geom_point(size = 0.5)
```

    ## Warning: Removed 1 rows containing missing values (`geom_point()`).

![](viz_part1_files/figure-gfm/unnamed-chunk-14-2.png)<!-- -->

## Saving plots

``` r
ggp_weather <-
  weather_df |> 
  ggplot(aes(x = tmin, y = tmax)) +
  geom_point()

ggp_weather
```

    ## Warning: Removed 17 rows containing missing values (`geom_point()`).

![](viz_part1_files/figure-gfm/unnamed-chunk-15-1.png)<!-- -->

``` r
ggsave("results/ggp_weather.pdf", ggp_weather)
```

    ## Saving 7 x 5 in image

    ## Warning: Removed 17 rows containing missing values (`geom_point()`).

## Embedding plots

``` r
ggp_weather
```

    ## Warning: Removed 17 rows containing missing values (`geom_point()`).

![](viz_part1_files/figure-gfm/unnamed-chunk-16-1.png)<!-- -->

``` r
# other options: fig.asp
```
