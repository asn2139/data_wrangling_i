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

## finding rows

Using the LotR data

First step is import each table

``` r
fellowship_ring=
  readxl:: read_excel("./data/LotR_Words.xlsx", range="B3:D6") %>% 
  mutate(movie="fellowship_ring")

two_towers=
  readxl:: read_excel("./data/LotR_Words.xlsx", range="F3:H6") %>% 
  mutate(movie="two_towers")

return_king=
  readxl:: read_excel("./data/LotR_Words.xlsx", range="J3:L6") %>% 
  mutate(movie="return_king")
```

Bind all the rows together

``` r
bind_rows(fellowship_ring, two_towers, return_king) %>% 
  janitor::clean_names() %>% 
  relocate (movie) %>% 
  pivot_longer(
    female:male,
    names_to="gender",
    values_to="words"
  )
```

    ## # A tibble: 18 x 4
    ##    movie           race   gender words
    ##    <chr>           <chr>  <chr>  <dbl>
    ##  1 fellowship_ring Elf    female  1229
    ##  2 fellowship_ring Elf    male     971
    ##  3 fellowship_ring Hobbit female    14
    ##  4 fellowship_ring Hobbit male    3644
    ##  5 fellowship_ring Man    female     0
    ##  6 fellowship_ring Man    male    1995
    ##  7 two_towers      Elf    female   331
    ##  8 two_towers      Elf    male     513
    ##  9 two_towers      Hobbit female     0
    ## 10 two_towers      Hobbit male    2463
    ## 11 two_towers      Man    female   401
    ## 12 two_towers      Man    male    3589
    ## 13 return_king     Elf    female   183
    ## 14 return_king     Elf    male     510
    ## 15 return_king     Hobbit female     2
    ## 16 return_king     Hobbit male    2673
    ## 17 return_king     Man    female   268
    ## 18 return_king     Man    male    2459
