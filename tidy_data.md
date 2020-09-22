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
lotr_tidy=
bind_rows(fellowship_ring, two_towers, return_king) %>% 
  janitor::clean_names() %>% 
  relocate (movie) %>% 
  pivot_longer(
    female:male,
    names_to="gender",
    values_to="words"
  )
```

\#\#Joining datasets

Import and clean the FAS datasets.

``` r
pups_df=
  read_csv("./data/FAS_pups.csv") %>% 
  janitor::clean_names() %>% 
  mutate(sex=recode(sex, `1` ="male", `2`="female"))
```

    ## Parsed with column specification:
    ## cols(
    ##   `Litter Number` = col_character(),
    ##   Sex = col_double(),
    ##   `PD ears` = col_double(),
    ##   `PD eyes` = col_double(),
    ##   `PD pivot` = col_double(),
    ##   `PD walk` = col_double()
    ## )

``` r
litters_df=
  read_csv("./data/FAS_litters.csv") %>% 
  janitor:: clean_names() %>% 
  relocate(litter_number) %>% 
  separate(group,into=c("dose", "day_of_tx"), sep=3)
```

    ## Parsed with column specification:
    ## cols(
    ##   Group = col_character(),
    ##   `Litter Number` = col_character(),
    ##   `GD0 weight` = col_double(),
    ##   `GD18 weight` = col_double(),
    ##   `GD of Birth` = col_double(),
    ##   `Pups born alive` = col_double(),
    ##   `Pups dead @ birth` = col_double(),
    ##   `Pups survive` = col_double()
    ## )

Next up, time to join them\!

``` r
fas_df=
  left_join(pups_df, litters_df, by="litter_number") %>% 
  arrange(litter_number) %>% 
  relocate(litter_number, dose, day_of_tx)
```
