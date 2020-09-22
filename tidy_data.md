Tidy Data
================

``` r
library (tidyverse)
```

    ## ── Attaching packages ─────────────────────────────────────────────────────────────────────────────────────────── tidyverse 1.3.0 ──

    ## ✓ ggplot2 3.3.2     ✓ purrr   0.3.4
    ## ✓ tibble  3.0.3     ✓ dplyr   1.0.2
    ## ✓ tidyr   1.1.2     ✓ stringr 1.4.0
    ## ✓ readr   1.3.1     ✓ forcats 0.5.0

    ## ── Conflicts ────────────────────────────────────────────────────────────────────────────────────────────── tidyverse_conflicts() ──
    ## x dplyr::filter() masks stats::filter()
    ## x dplyr::lag()    masks stats::lag()

## `pivot_longer`

``` r
pulse_data =
  haven:: read_sas("./data/public_pulse_data.sas7bdat") %>% 
  janitor:: clean_names()
```

Wide format to long format….

``` r
pulse_data_tidy=
  pulse_data %>% 
  pivot_longer(
    bdi_score_bl:bdi_score_12m, 
    names_to= "visit",
    names_prefix="bdi_score_",
    values_to="bdi"
  )
```

rewrite, combine extend (to add a mutate)

``` r
pulse_data =
  haven:: read_sas("./data/public_pulse_data.sas7bdat") %>% 
  janitor:: clean_names() %>% 
  pivot_longer(
    bdi_score_bl:bdi_score_12m, 
    names_to= "visit",
    names_prefix="bdi_score_",
    values_to="bdi"
) %>% 
  relocate(id,visit) %>% 
  mutate(visit= recode(visit, "bl"="00m"))
```

## `pivot_wider`

Make up some data

``` r
analysis_result=
  tibble(
    group=c("treatment", "treatment", "placebo", "placebo"),
    time=c("pre", "post", "pre", "post"),
    mean=c(4, 8, 3.5, 4)
  )

analysis_result %>% 
  pivot_wider(
    names_from="time",
    values_from="mean"
  )
```

    ## # A tibble: 2 x 3
    ##   group       pre  post
    ##   <chr>     <dbl> <dbl>
    ## 1 treatment   4       8
    ## 2 placebo     3.5     4
