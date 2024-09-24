Tidy Data
================

I’m an R Markdown document!

This document will show how to tidy data.

## Pivot longer to wider

``` r
pulse_df =
  read_sas("data/public_pulse_data.sas7bdat") %>% 
  janitor::clean_names()
```

This needs to go from wide to long format.

``` r
pulse_tidy_df =
  pulse_df %>% 
  pivot_longer(
    cols = bdi_score_bl:bdi_score_12m,
    names_to = "visit",
    values_to = "bdi_score",
    names_prefix = "bdi_score_"
  ) %>% 
  mutate(
    visit = replace(visit,visit == "bl","00m")
         ) %>%
  relocate(id,visit) 
```

One more example:

``` r
litters_df =
  read_csv("data/FAS_litters.csv",na = c("NA","",".")) %>% 
  janitor::clean_names()
```

    ## Rows: 49 Columns: 8
    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ","
    ## chr (2): Group, Litter Number
    ## dbl (6): GD0 weight, GD18 weight, GD of Birth, Pups born alive, Pups dead @ ...
    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

``` r
(litters_tidy_df =
  litters_df %>% 
  pivot_longer(
    cols = gd0_weight:gd18_weight,
    names_to = "gd_time",
    values_to = "weight"
  ) %>% 
    mutate(
      gd_time = case_match(
        gd_time,
        "gd0_weight" ~ 0,
        "gd18_weight" ~ 18
      )
      )
  
  )
```

    ## # A tibble: 98 × 8
    ##    group litter_number gd_of_birth pups_born_alive pups_dead_birth pups_survive
    ##    <chr> <chr>               <dbl>           <dbl>           <dbl>        <dbl>
    ##  1 Con7  #85                    20               3               4            3
    ##  2 Con7  #85                    20               3               4            3
    ##  3 Con7  #1/2/95/2              19               8               0            7
    ##  4 Con7  #1/2/95/2              19               8               0            7
    ##  5 Con7  #5/5/3/83/3-3          19               6               0            5
    ##  6 Con7  #5/5/3/83/3-3          19               6               0            5
    ##  7 Con7  #5/4/2/95/2            19               5               1            4
    ##  8 Con7  #5/4/2/95/2            19               5               1            4
    ##  9 Con7  #4/2/95/3-3            20               6               0            6
    ## 10 Con7  #4/2/95/3-3            20               6               0            6
    ## # ℹ 88 more rows
    ## # ℹ 2 more variables: gd_time <dbl>, weight <dbl>

## Pivot wider

Make up an analysis result table.

``` r
ana_df = 
  tibble(
    group = c("trt","trt","control","control"),
    time = c("pre","post","pre","post"),
    mean= c(4,10,4.2,5)
  )
```

Pivot wider for human readability

``` r
ana_df %>% 
  pivot_wider(
    names_from = time,
    values_from = mean
  )
```

    ## # A tibble: 2 × 3
    ##   group     pre  post
    ##   <chr>   <dbl> <dbl>
    ## 1 trt       4      10
    ## 2 control   4.2     5
