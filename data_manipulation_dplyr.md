data_manipulation_dplyr
================
Jiayi Shi
2022-09-22

Pipes:%\>% “and then”, tibble goes in, tibble comes out

``` r
litters_data = read_csv('data/FAS_litters.csv')
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
litters_data = janitor::clean_names(litters_data)

pups_data = read_csv("./data/FAS_pups.csv",col_types = "ciiiii")
pups_data = janitor::clean_names(pups_data)
```

## Select

``` r
select(litters_data,1:5)
```

    ## # A tibble: 49 × 5
    ##    group litter_number   gd0_weight gd18_weight gd_of_birth
    ##    <chr> <chr>                <dbl>       <dbl>       <dbl>
    ##  1 Con7  #85                   19.7        34.7          20
    ##  2 Con7  #1/2/95/2             27          42            19
    ##  3 Con7  #5/5/3/83/3-3         26          41.4          19
    ##  4 Con7  #5/4/2/95/2           28.5        44.1          19
    ##  5 Con7  #4/2/95/3-3           NA          NA            20
    ##  6 Con7  #2/2/95/3-2           NA          NA            20
    ##  7 Con7  #1/5/3/83/3-3/2       NA          NA            20
    ##  8 Con8  #3/83/3-3             NA          NA            20
    ##  9 Con8  #2/95/3               NA          NA            20
    ## 10 Con8  #3/5/2/2/95           28.5        NA            20
    ## # … with 39 more rows

``` r
#select(litters_data, group, litter_number, gd0_weight, pups_born_alive)
#select(litters_data,group:gd_of_birth)
#select(litters_data,group,gd_of_birth)
#specify columns you’d like to remove:select(litters_data, -group,-pups_survive) 
```

-   Rename(won’t change the original dataset, unless litters_data=…)

``` r
#select(df, New_name = initial_name)#method1
select(litters_data,GROUP = group)
```

    ## # A tibble: 49 × 1
    ##    GROUP
    ##    <chr>
    ##  1 Con7 
    ##  2 Con7 
    ##  3 Con7 
    ##  4 Con7 
    ##  5 Con7 
    ##  6 Con7 
    ##  7 Con7 
    ##  8 Con8 
    ##  9 Con8 
    ## 10 Con8 
    ## # … with 39 more rows

``` r
rename(litters_data, GROUP = group)#method2
```

    ## # A tibble: 49 × 8
    ##    GROUP litter_number   gd0_weight gd18_weight gd_of_…¹ pups_…² pups_…³ pups_…⁴
    ##    <chr> <chr>                <dbl>       <dbl>    <dbl>   <dbl>   <dbl>   <dbl>
    ##  1 Con7  #85                   19.7        34.7       20       3       4       3
    ##  2 Con7  #1/2/95/2             27          42         19       8       0       7
    ##  3 Con7  #5/5/3/83/3-3         26          41.4       19       6       0       5
    ##  4 Con7  #5/4/2/95/2           28.5        44.1       19       5       1       4
    ##  5 Con7  #4/2/95/3-3           NA          NA         20       6       0       6
    ##  6 Con7  #2/2/95/3-2           NA          NA         20       6       0       4
    ##  7 Con7  #1/5/3/83/3-3/2       NA          NA         20       9       0       9
    ##  8 Con8  #3/83/3-3             NA          NA         20       9       1       8
    ##  9 Con8  #2/95/3               NA          NA         20       8       0       8
    ## 10 Con8  #3/5/2/2/95           28.5        NA         20       8       0       8
    ## # … with 39 more rows, and abbreviated variable names ¹​gd_of_birth,
    ## #   ²​pups_born_alive, ³​pups_dead_birth, ⁴​pups_survive

-   `starts_with()`, `ends_with()`, and `contains()`  
    Useful functions in select, especially when variables are named with
    suffixes or other standard patterns.

``` r
select(litters_data, starts_with("gd"))
```

    ## # A tibble: 49 × 3
    ##    gd0_weight gd18_weight gd_of_birth
    ##         <dbl>       <dbl>       <dbl>
    ##  1       19.7        34.7          20
    ##  2       27          42            19
    ##  3       26          41.4          19
    ##  4       28.5        44.1          19
    ##  5       NA          NA            20
    ##  6       NA          NA            20
    ##  7       NA          NA            20
    ##  8       NA          NA            20
    ##  9       NA          NA            20
    ## 10       28.5        NA            20
    ## # … with 39 more rows

``` r
select(litters_data, ends_with("weight"))
```

    ## # A tibble: 49 × 2
    ##    gd0_weight gd18_weight
    ##         <dbl>       <dbl>
    ##  1       19.7        34.7
    ##  2       27          42  
    ##  3       26          41.4
    ##  4       28.5        44.1
    ##  5       NA          NA  
    ##  6       NA          NA  
    ##  7       NA          NA  
    ##  8       NA          NA  
    ##  9       NA          NA  
    ## 10       28.5        NA  
    ## # … with 39 more rows

``` r
select(litters_data, contains("pups"))
```

    ## # A tibble: 49 × 3
    ##    pups_born_alive pups_dead_birth pups_survive
    ##              <dbl>           <dbl>        <dbl>
    ##  1               3               4            3
    ##  2               8               0            7
    ##  3               6               0            5
    ##  4               5               1            4
    ##  5               6               0            6
    ##  6               6               0            4
    ##  7               9               0            9
    ##  8               9               1            8
    ##  9               8               0            8
    ## 10               8               0            8
    ## # … with 39 more rows

\*`everything()`,\`relocate()\`\`  
Pulling first (reorganizing columns)

``` r
select(litters_data, litter_number, pups_survive, everything())#method1
```

    ## # A tibble: 49 × 8
    ##    litter_number   pups_survive group gd0_weight gd18_…¹ gd_of…² pups_…³ pups_…⁴
    ##    <chr>                  <dbl> <chr>      <dbl>   <dbl>   <dbl>   <dbl>   <dbl>
    ##  1 #85                        3 Con7        19.7    34.7      20       3       4
    ##  2 #1/2/95/2                  7 Con7        27      42        19       8       0
    ##  3 #5/5/3/83/3-3              5 Con7        26      41.4      19       6       0
    ##  4 #5/4/2/95/2                4 Con7        28.5    44.1      19       5       1
    ##  5 #4/2/95/3-3                6 Con7        NA      NA        20       6       0
    ##  6 #2/2/95/3-2                4 Con7        NA      NA        20       6       0
    ##  7 #1/5/3/83/3-3/2            9 Con7        NA      NA        20       9       0
    ##  8 #3/83/3-3                  8 Con8        NA      NA        20       9       1
    ##  9 #2/95/3                    8 Con8        NA      NA        20       8       0
    ## 10 #3/5/2/2/95                8 Con8        28.5    NA        20       8       0
    ## # … with 39 more rows, and abbreviated variable names ¹​gd18_weight,
    ## #   ²​gd_of_birth, ³​pups_born_alive, ⁴​pups_dead_birth

``` r
relocate(litters_data, litter_number, pups_survive)#method2
```

    ## # A tibble: 49 × 8
    ##    litter_number   pups_survive group gd0_weight gd18_…¹ gd_of…² pups_…³ pups_…⁴
    ##    <chr>                  <dbl> <chr>      <dbl>   <dbl>   <dbl>   <dbl>   <dbl>
    ##  1 #85                        3 Con7        19.7    34.7      20       3       4
    ##  2 #1/2/95/2                  7 Con7        27      42        19       8       0
    ##  3 #5/5/3/83/3-3              5 Con7        26      41.4      19       6       0
    ##  4 #5/4/2/95/2                4 Con7        28.5    44.1      19       5       1
    ##  5 #4/2/95/3-3                6 Con7        NA      NA        20       6       0
    ##  6 #2/2/95/3-2                4 Con7        NA      NA        20       6       0
    ##  7 #1/5/3/83/3-3/2            9 Con7        NA      NA        20       9       0
    ##  8 #3/83/3-3                  8 Con8        NA      NA        20       9       1
    ##  9 #2/95/3                    8 Con8        NA      NA        20       8       0
    ## 10 #3/5/2/2/95                8 Con8        28.5    NA        20       8       0
    ## # … with 39 more rows, and abbreviated variable names ¹​gd18_weight,
    ## #   ²​gd_of_birth, ³​pups_born_alive, ⁴​pups_dead_birth

## filter

filter(df,condition) gd_of_birth == 20 pups_born_alive \>= 2
pups_survive != 4 group %in% c(“Con7”, “Con8”) group == “Con7” &
gd_of_birth == 20

### drop missing value

!(is.na(gd0_weight))  
Preferred:  
drop_na(litters_data) will remove any row with a missing value.  
drop_na(litters_data, wt_increase) will remove rows for which
wt_increase is missing.
