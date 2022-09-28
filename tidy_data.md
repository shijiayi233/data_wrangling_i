Tidy_data
================
Jiayi SHi
2022-09-27

## Join

inner joins (intersection)  
Outer joins:left join, right join, full join (union)

## Key Functions

For tidying table:  
`pivot_longer`  
`pivot_wider`  
`separate`  
For combining multiple tables:  
`bind_rows`  
`*_join`

``` r
library(tidyverse)

options(tibble.print_min=5)
```

## pivot_longer

The package::function syntax lets you use a function from a package
without loading the whole library.

``` r
pulse_df = haven::read_sas('data/public_pulse_data.sas7bdat') %>% 
  janitor::clean_names()

pulse_tidy_data = 
  pivot_longer(
    pulse_df,
    4:7,
    names_to = 'visit', #create a variable named by names_to
    names_prefix = 'bdi_score_',  
    values_to = 'bdi'
  ) %>% 
  mutate(
    visit = replace(visit,visit == 'bl', '00m'), #replace(x,list,values)x: Name of vector; list: Elements to replace; values: Replacement values
    visit = factor(visit)
#data type: factor, has certain ordering
  ) %>% 
  arrange(id,visit)
```

### Assessment

- recode

``` r
litters = read_csv('data/FAS_litters.csv') %>% 
  janitor::clean_names() %>% 
  select(litter_number,ends_with('weight')) %>% 
  pivot_longer(
    2:3,
    names_to = 'gd',
    values_to = 'weight'
  ) %>% 
  mutate(
    gd = recode(gd, 'gd0_weight' = 0,'gd18_weight'= 18)
  )#recode(.x, old=new) .x is a vector to modify
```

    ## Rows: 49 Columns: 8
    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ","
    ## chr (2): Group, Litter Number
    ## dbl (6): GD0 weight, GD18 weight, GD of Birth, Pups born alive, Pups dead @ ...
    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

- if_else

``` r
litters = read_csv('data/FAS_litters.csv') %>% 
  janitor::clean_names() %>% 
  select(litter_number,ends_with('weight')) %>% 
  pivot_longer(
    2:3,
    names_to = 'gd',
    values_to = 'weight'
  ) %>% 
  mutate(
    gd = if_else(.$gd=='gd0_weight',0,18)
  )
```

    ## Rows: 49 Columns: 8
    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ","
    ## chr (2): Group, Litter Number
    ## dbl (6): GD0 weight, GD18 weight, GD of Birth, Pups born alive, Pups dead @ ...
    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

- replace

``` r
litters = read_csv('data/FAS_litters.csv') %>% 
  janitor::clean_names() %>% 
  select(litter_number,ends_with('weight')) %>% 
  pivot_longer(
    2:3,
    names_to = 'gd',
    values_to = 'weight'
  ) %>% 
  mutate(gd = replace(gd, gd=='gd0_weight', 0)) %>% 
  mutate(gd = replace(gd, gd=='gd18_weight', 18))
```

    ## Rows: 49 Columns: 8
    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ","
    ## chr (2): Group, Litter Number
    ## dbl (6): GD0 weight, GD18 weight, GD of Birth, Pups born alive, Pups dead @ ...
    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

## pivot_wider

``` r
analysis_result = tibble(
  group = c("treatment", "treatment", "placebo", "placebo"),
  time = c("pre", "post", "pre", "post"),
  mean = c(4, 8, 3.5, 4)
)

analysis_result_wide = pivot_wider(
  analysis_result,
  names_from = 'time',
  values_from = 'mean'
    )
```

## bind_rows

``` r
fellowship_king = 
  readxl::read_excel('data/LotR_Words.xlsx',range='B3:D6') %>% 
  mutate(movie = "fellowship_ring")

two_towers = 
  readxl::read_excel("./data/LotR_Words.xlsx", range = "F3:H6") %>%
  mutate(movie = "two_towers")

return_king = 
  readxl::read_excel("./data/LotR_Words.xlsx", range = "J3:L6") %>%
  mutate(movie = "return_king")

lotr_tidy = bind_rows(fellowship_king,two_towers,return_king) %>% 
  janitor::clean_names() %>% 
  pivot_longer(
    female:male,
    names_to = "gender",
    values_to = "words"
  ) %>% 
  mutate(race = str_to_lower(race))
```

## \*\_join

``` r
pups = read_csv("data/FAS_pups.csv") %>% 
  janitor::clean_names() %>% 
  mutate(
    sex = recode(sex,`1`='male',`2`='female'),
    # backquote for number, regular quote for character 
  )
```

    ## Rows: 313 Columns: 6
    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ","
    ## chr (1): Litter Number
    ## dbl (5): Sex, PD ears, PD eyes, PD pivot, PD walk
    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

``` r
litters = read_csv("data/FAS_litters.csv") %>% 
  janitor::clean_names() %>% 
  separate(group,into = c("dose","day_of_treatment"),sep = 3) %>% 
  relocate(litter_number) %>% 
  mutate(
    wt_gain = gd18_weight-gd0_weight,
    dose = str_to_lower(dose)
  )
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
fas_data = left_join(pups,litters,by = "litter_number")
```

### Assessment

``` r
surv_os = read_csv("data/surv_os.csv") %>% 
  setNames(c("uni","os"))
```

    ## Rows: 173 Columns: 2
    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ","
    ## chr (2): What is your UNI?, What operating system do you use?
    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

``` r
# change column names: setNames(df,c(newnamecol1,newnamecol2,...))
surv_prog = read_csv("data/surv_program_git.csv") %>% 
  setNames(c("uni","prog","exp"))
```

    ## Rows: 135 Columns: 3
    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ","
    ## chr (3): What is your UNI?, What is your degree program?, Which most accurat...
    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

``` r
surv = full_join(surv_os,surv_prog,by = "uni")
```

``` r
surv_os = read_csv("data/surv_os.csv") %>% 
  janitor::clean_names() %>% #不仅变小写，而且加下划线
  rename(id = what_is_your_uni, os = what_operating_system_do_you_use)
```

    ## Rows: 173 Columns: 2
    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ","
    ## chr (2): What is your UNI?, What operating system do you use?
    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

``` r
surv_pr_git = read_csv("data/surv_program_git.csv") %>% 
  janitor::clean_names() %>% 
  rename(
    id = what_is_your_uni, 
    prog = what_is_your_degree_program,
    git_exp = which_most_accurately_describes_your_experience_with_git)
```

    ## Rows: 135 Columns: 3
    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ","
    ## chr (3): What is your UNI?, What is your degree program?, Which most accurat...
    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

``` r
left_join(surv_os, surv_pr_git)
```

    ## Joining, by = "id"

    ## # A tibble: 175 × 4
    ##   id          os         prog  git_exp                                          
    ##   <chr>       <chr>      <chr> <chr>                                            
    ## 1 student_87  <NA>       MS    Pretty smooth: needed some work to connect Git, …
    ## 2 student_106 Windows 10 Other Pretty smooth: needed some work to connect Git, …
    ## 3 student_66  Mac OS X   MPH   Smooth: installation and connection with GitHub …
    ## 4 student_93  Windows 10 MS    Smooth: installation and connection with GitHub …
    ## 5 student_99  Mac OS X   MS    Smooth: installation and connection with GitHub …
    ## # … with 170 more rows

``` r
inner_join(surv_os, surv_pr_git)
```

    ## Joining, by = "id"

    ## # A tibble: 129 × 4
    ##   id          os         prog  git_exp                                          
    ##   <chr>       <chr>      <chr> <chr>                                            
    ## 1 student_87  <NA>       MS    Pretty smooth: needed some work to connect Git, …
    ## 2 student_106 Windows 10 Other Pretty smooth: needed some work to connect Git, …
    ## 3 student_66  Mac OS X   MPH   Smooth: installation and connection with GitHub …
    ## 4 student_93  Windows 10 MS    Smooth: installation and connection with GitHub …
    ## 5 student_99  Mac OS X   MS    Smooth: installation and connection with GitHub …
    ## # … with 124 more rows

``` r
anti_join(surv_os, surv_pr_git)
```

    ## Joining, by = "id"

    ## # A tibble: 46 × 2
    ##   id          os        
    ##   <chr>       <chr>     
    ## 1 student_86  Mac OS X  
    ## 2 student_91  Windows 10
    ## 3 student_24  Mac OS X  
    ## 4 student_103 Mac OS X  
    ## 5 student_163 Mac OS X  
    ## # … with 41 more rows

``` r
# anti_join(x, y, by = NULL, copy = FALSE, ...): return all rows from x without a match in y.
anti_join(surv_pr_git, surv_os)
```

    ## Joining, by = "id"

    ## # A tibble: 15 × 3
    ##    id         prog  git_exp                                                     
    ##    <chr>      <chr> <chr>                                                       
    ##  1 <NA>       MPH   "Pretty smooth: needed some work to connect Git, GitHub, an…
    ##  2 student_17 PhD   "Pretty smooth: needed some work to connect Git, GitHub, an…
    ##  3 <NA>       MPH   "Pretty smooth: needed some work to connect Git, GitHub, an…
    ##  4 <NA>       MPH   "Pretty smooth: needed some work to connect Git, GitHub, an…
    ##  5 <NA>       MS    "Pretty smooth: needed some work to connect Git, GitHub, an…
    ##  6 student_53 MS    "Pretty smooth: needed some work to connect Git, GitHub, an…
    ##  7 <NA>       MS    "Smooth: installation and connection with GitHub was easy"  
    ##  8 student_80 PhD   "Pretty smooth: needed some work to connect Git, GitHub, an…
    ##  9 student_16 MPH   "Smooth: installation and connection with GitHub was easy"  
    ## 10 student_98 MS    "Smooth: installation and connection with GitHub was easy"  
    ## 11 <NA>       MS    "Pretty smooth: needed some work to connect Git, GitHub, an…
    ## 12 <NA>       MS    "What's \"Git\" ...?"                                       
    ## 13 <NA>       MS    "Smooth: installation and connection with GitHub was easy"  
    ## 14 <NA>       MPH   "Pretty smooth: needed some work to connect Git, GitHub, an…
    ## 15 <NA>       MS    "Pretty smooth: needed some work to connect Git, GitHub, an…
