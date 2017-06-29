Import Data
================
Created: 2017-05-17 <br> Updated: 2017-06-26

``` r
# Load packages
library(tidyverse)
library(readxl)
library(feather)

# devtools::install_github("brad-cannell/my_functions")
library(myFunctions)
```

### Klarus patient list

``` r
col_names <- c(
  "id", "gender", "zip", "soc", "start_date", "end_date", "primary_diagnosis", "secondary_diagnosis"
)

klarus <- read_excel(path = "../data/klarus_patient_list.xlsx", 
                     sheet = "klarus_patient_list",
                     col_names = col_names,
                     skip = 1)

about_data(klarus) # 2,524 observations and 8 variables
```

    ## 2,524 observations and 8 variables

``` r
write_feather(klarus, path = "../data/klarus.feather")
```

### MedStar patient list

``` r
medstar <- read_excel(path = "../data/medstar_patient_list.xlsx", 
                     sheet = "medstar_patient_list",
                     col_names = c("id", "enrollment_date", "graduation_date", "death_date"),
                     skip = 1)

medstar$death_date <- as.POSIXct(medstar$death_date, format = "%m/%d/%Y")

about_data(medstar) # 627 observations and 4 variables
```

    ## 627 observations and 4 variables

``` r
write_feather(medstar, path = "../data/medstar.feather")
```

### Patient 911 calls

``` r
col_names <- c(
  "id", "enrollment_id", "age", "encounter_date", "encounter_type", "determinant", "call_priority",
  "response_number", "incident_number", "ems_encounter_outcome", "transported", "mhp_responded", "mhp_on_scene",
  "vehicle_unit", "mhp_no_os_preventable", "no_ccp_available", "requested_transport", "client_other_address",
  "crew_trans_prior_ccp", "ccp_consult_prior_trans", "mhp_one", "mhp_two", "receiving_facility", 
  "encounter_notes", "comm_review_outcome"
)

calls <- read_excel(path = "../data/patient_911_calls.xlsx", 
                     sheet = "patient_911_calls",
                     col_names = col_names,
                     skip = 1)

about_data(calls) # 1,217 observations and 25 variables
```

    ## 1,217 observations and 25 variables

``` r
write_feather(calls, path = "../data/calls.feather")
```

------------------------------------------------------------------------

 

#### Session Info:

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
    ##  [1] myFunctions_0.1.0 feather_0.3.1     readxl_1.0.0     
    ##  [4] dplyr_0.7.0       purrr_0.2.2.2     readr_1.1.1      
    ##  [7] tidyr_0.6.3       tibble_1.3.3      ggplot2_2.2.1    
    ## [10] tidyverse_1.1.1  
    ## 
    ## loaded via a namespace (and not attached):
    ##  [1] Rcpp_0.12.10     cellranger_1.1.0 compiler_3.4.0   plyr_1.8.4      
    ##  [5] forcats_0.2.0    tools_3.4.0      digest_0.6.12    lubridate_1.6.0 
    ##  [9] jsonlite_1.4     evaluate_0.10    nlme_3.1-131     gtable_0.2.0    
    ## [13] lattice_0.20-35  rlang_0.1.1      psych_1.7.5      yaml_2.1.14     
    ## [17] parallel_3.4.0   haven_1.0.0      xml2_1.1.1       stringr_1.2.0   
    ## [21] httr_1.2.1       knitr_1.16       hms_0.3          rprojroot_1.2   
    ## [25] grid_3.4.0       glue_1.1.0       R6_2.2.0         foreign_0.8-67  
    ## [29] rmarkdown_1.6    modelr_0.1.0     reshape2_1.4.2   magrittr_1.5    
    ## [33] backports_1.0.5  scales_0.4.1     htmltools_0.3.6  rvest_0.3.2     
    ## [37] assertthat_0.2.0 mnormt_1.5-5     colorspace_1.3-2 stringi_1.1.5   
    ## [41] lazyeval_0.2.0   munsell_0.4.3    broom_0.4.2
