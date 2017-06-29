Data Cleaning
================
Created: 2017-05-17 <br> Updated: 2017-06-26

``` r
# Load packages
library(tidyverse)
library(feather)
library(forcats)

# devtools::install_github("brad-cannell/my_functions")
library(myFunctions)
```

``` r
calls   <- read_feather("../data/calls.feather")
klarus  <- read_feather("../data/klarus.feather")
medstar <- read_feather("../data/medstar.feather")
```

Need to join the Klarus patient list to 911 calls using:
\* id
\* 911 encounter date needs to fall within Klarus start and end date.
\* Every row in the 911 data should have a matching row in the Klarus data. If not, I need to let Daniel know the id, response \#, and incident \#.

To check for rows in the 911 data that don't have a match in the Klarus data, I'm going to give each row a sequential number. I'm also going to create a vector that is equal to 1 through the number of rows in the calls data. After merging the two data sets, I will check for missing row numbers.

``` r
calls$row <- 1:nrow(calls)
check <- 1:nrow(calls)
```

``` r
# Attach calls to the patient list
klarus_calls <- left_join(klarus, calls, by = "id")
```

Look for rows from the 911 calls data that don't exist in the merged data.

``` r
merged_rows <- klarus_calls$row %>% unique %>% sort
no_match <- setdiff(check, merged_rows)
no_match
```

    ##  [1]   20   71  156  164  170  186  275  286  377  412  414  422  539  563
    ## [15]  565  576  659  796  838  897  941  994 1000 1004

There are 24 rows that appear in the 911 calls data, but not in the klarus data. I'm going to export these rows as a csv file, and send to Daniel for confirmation.

``` r
# no_match_rows <- filter(calls, row %in% no_match)
# no_match_rows
```

``` r
# write_csv(no_match_rows, "../data/no_match_rows.csv")
```

**2017-06-23 from Daniel Ebbett:**

> Dan Bruce called me regarding the missing rows today. These 911 calls can be excluded from the review. If you want the more in depth explanation let me know and I can give you a call to give you the info.

These 24 911 calls are already missing from the joined data. Nothing else needs to be done.

#### Filtering by dates

Now the 911 calls are joined to the Klarus patient list by ID. However, I only want to keep the rows where the 911 encounter date falls within Klarus start and end date.

``` r
klarus_calls <- filter(klarus_calls, start_date <= encounter_date & encounter_date <= end_date)
about_data(klarus_calls) # 374 observations and 33 variables
```

    ## 374 observations and 33 variables

#### Merge with death dates

``` r
medstar_death <- select(medstar, id, death_date) %>% filter(!is.na(death_date))
medstar_death$id %>% unique() %>% length() # 36. No duplicate ids.
```

    ## [1] 36

``` r
klarus_calls <- left_join(klarus_calls, medstar_death, by = "id")
about_data(klarus_calls) # 374 observations and 34 variables
```

    ## 374 observations and 34 variables

``` r
# Save progress
write_feather(klarus_calls, "../data/klarus_calls.feather")
```

------------------------------------------------------------------------

Variable Management
===================

``` r
klarus_calls <- read_feather("../data/klarus_calls.feather")
about_data(klarus_calls) # 374 observations and 34 variables
```

    ## 374 observations and 34 variables

Recode call priority

``` r
klarus_calls$call_priority <- fct_collapse(klarus_calls$call_priority,
  "1" = c("1", "P1", "C1"),
  "2" = c("2", "P2", "C2"),
  "3" = c("3", "P3", "C3")
)
```

    ## Warning: Unknown levels in `f`: C1, C3

Factorize selected character variables

``` r
vars <- c("gender", "zip", "primary_diagnosis", "secondary_diagnosis", "encounter_type", "encounter_type",
          "determinant", "ems_encounter_outcome", "transported", "mhp_on_scene", "vehicle_unit",
          "mhp_no_os_preventable", "mhp_one", "mhp_two", "receiving_facility")


klarus_calls <- klarus_calls %>% 
  map_at(., vars, function(x) {
    x <- factor(x)
    x
  }) %>% 
  as_tibble()

about_data(klarus_calls) # 374 observations and 34 variables
```

    ## 374 observations and 34 variables

``` r
# Save progress
write_feather(klarus_calls, "../data/klarus_calls.feather")
```

    ## R version 3.4.0 (2017-04-21)
    ## Platform: x86_64-apple-darwin15.6.0 (64-bit)
    ## Running under: macOS Sierra 10.12.5
    ## 
    ## Matrix products: default
    ## BLAS: /Library/Frameworks/R.framework/Versions/3.4/Resources/lib/libRblas.0.dylib
    ## LAPACK: /Library/Frameworks/R.framework/Versions/3.4/Resources/lib/libRlapack.dylib
    ## 
    ## locale:
    ## [1] en_US.UTF-8/en_US.UTF-8/en_US.UTF-8/C/en_US.UTF-8/en_US.UTF-8
    ## 
    ## attached base packages:
    ## [1] stats     graphics  grDevices utils     datasets  methods   base     
    ## 
    ## other attached packages:
    ##  [1] bindrcpp_0.1      myFunctions_0.1.0 forcats_0.2.0    
    ##  [4] feather_0.3.1     dplyr_0.7.0       purrr_0.2.2.2    
    ##  [7] readr_1.1.1       tidyr_0.6.3       tibble_1.3.3     
    ## [10] ggplot2_2.2.1     tidyverse_1.1.1  
    ## 
    ## loaded via a namespace (and not attached):
    ##  [1] Rcpp_0.12.10     bindr_0.1        cellranger_1.1.0 compiler_3.4.0  
    ##  [5] plyr_1.8.4       tools_3.4.0      digest_0.6.12    lubridate_1.6.0 
    ##  [9] jsonlite_1.4     evaluate_0.10    nlme_3.1-131     gtable_0.2.0    
    ## [13] lattice_0.20-35  pkgconfig_2.0.1  rlang_0.1.1      psych_1.7.5     
    ## [17] yaml_2.1.14      parallel_3.4.0   haven_1.0.0      xml2_1.1.1      
    ## [21] stringr_1.2.0    httr_1.2.1       knitr_1.16       hms_0.3         
    ## [25] rprojroot_1.2    grid_3.4.0       glue_1.1.0       R6_2.2.0        
    ## [29] readxl_1.0.0     foreign_0.8-67   rmarkdown_1.6    modelr_0.1.0    
    ## [33] reshape2_1.4.2   magrittr_1.5     backports_1.0.5  scales_0.4.1    
    ## [37] htmltools_0.3.6  rvest_0.3.2      assertthat_0.2.0 mnormt_1.5-5    
    ## [41] colorspace_1.3-2 stringi_1.1.5    lazyeval_0.2.0   munsell_0.4.3   
    ## [45] broom_0.4.2
