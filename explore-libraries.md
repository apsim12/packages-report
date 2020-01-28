explore-libraries.R
================
ArielSim
2020-01-27

Which libraries does R search for
    packages?

``` r
.libPaths()
```

    ## [1] "/Library/Frameworks/R.framework/Versions/3.6/Resources/library"

``` r
## let's confirm the second element is, in fact, the default library
.Library
```

    ## [1] "/Library/Frameworks/R.framework/Resources/library"

``` r
identical(.Library, .libPaths()[2])
```

    ## [1] FALSE

``` r
## Huh? Maybe this is an symbolic link issue?
library(fs)
identical(path_real(.Library), path_real(.libPaths()[2]))
```

    ## [1] FALSE

Installed
    packages

``` r
library(tidyverse)
```

    ## ── Attaching packages ────────────────────────────────── tidyverse 1.3.0 ──

    ## ✓ ggplot2 3.2.1     ✓ purrr   0.3.3
    ## ✓ tibble  2.1.3     ✓ dplyr   0.8.3
    ## ✓ tidyr   1.0.2     ✓ stringr 1.4.0
    ## ✓ readr   1.3.1     ✓ forcats 0.4.0

    ## ── Conflicts ───────────────────────────────────── tidyverse_conflicts() ──
    ## x dplyr::filter() masks stats::filter()
    ## x dplyr::lag()    masks stats::lag()

``` r
ipt <- installed.packages() %>%
  as_tibble()

## how many packages?
nrow(ipt)
```

    ## [1] 148

Exploring the packages

``` r
## count some things! inspiration
##   * tabulate by LibPath, Priority, or both
ipt %>%
  count(LibPath, Priority)
```

    ## # A tibble: 3 x 3
    ##   LibPath                                                       Priority       n
    ##   <chr>                                                         <chr>      <int>
    ## 1 /Library/Frameworks/R.framework/Versions/3.6/Resources/libra… base          14
    ## 2 /Library/Frameworks/R.framework/Versions/3.6/Resources/libra… recommend…    15
    ## 3 /Library/Frameworks/R.framework/Versions/3.6/Resources/libra… <NA>         119

``` r
##   * what proportion need compilation?
ipt %>%
  count(NeedsCompilation) %>%
  mutate(prop = n / sum(n))
```

    ## # A tibble: 3 x 3
    ##   NeedsCompilation     n   prop
    ##   <chr>            <int>  <dbl>
    ## 1 no                  70 0.473 
    ## 2 yes                 73 0.493 
    ## 3 <NA>                 5 0.0338

``` r
##   * how break down re: version of R they were built on
ipt %>%
  count(Built) %>%
  mutate(prop = n / sum(n))
```

    ## # A tibble: 2 x 3
    ##   Built     n  prop
    ##   <chr> <int> <dbl>
    ## 1 3.6.0   112 0.757
    ## 2 3.6.2    36 0.243

Reflections

``` r
## reflect on ^^ and make a few notes to yourself; inspiration
##   * does the number of base + recommended packages make sense to you?
##   * how does the result of .libPaths() relate to the result of .Library?
```

Going further

``` r
## if you have time to do more ...

## is every package in .Library either base or recommended?
all_default_pkgs <- list.files(.Library)
all_br_pkgs <- ipt %>%
  filter(Priority %in% c("base", "recommended")) %>%
  pull(Package)
setdiff(all_default_pkgs, all_br_pkgs)
```

    ##   [1] "askpass"      "assertthat"   "backports"    "base64enc"    "BH"          
    ##   [6] "brew"         "broom"        "callr"        "cellranger"   "cli"         
    ##  [11] "clipr"        "clisymbols"   "colorspace"   "commonmark"   "covr"        
    ##  [16] "crayon"       "crosstalk"    "curl"         "data.table"   "DBI"         
    ##  [21] "dbplyr"       "desc"         "devtools"     "digest"       "dplyr"       
    ##  [26] "DT"           "ellipsis"     "evaluate"     "fansi"        "farver"      
    ##  [31] "fastmap"      "forcats"      "fs"           "generics"     "ggplot2"     
    ##  [36] "gh"           "git2r"        "glue"         "gtable"       "haven"       
    ##  [41] "here"         "highr"        "hms"          "htmltools"    "htmlwidgets" 
    ##  [46] "httpuv"       "httr"         "ini"          "jsonlite"     "knitr"       
    ##  [51] "labeling"     "later"        "lazyeval"     "lifecycle"    "lubridate"   
    ##  [56] "magrittr"     "markdown"     "memoise"      "mime"         "modelr"      
    ##  [61] "munsell"      "openssl"      "pillar"       "pkgbuild"     "pkgconfig"   
    ##  [66] "pkgload"      "plogr"        "plyr"         "praise"       "prettyunits" 
    ##  [71] "processx"     "progress"     "promises"     "ps"           "purrr"       
    ##  [76] "R6"           "rcmdcheck"    "RColorBrewer" "Rcpp"         "readr"       
    ##  [81] "readxl"       "rematch"      "remotes"      "reprex"       "repurrrsive" 
    ##  [86] "reshape2"     "rex"          "rlang"        "rmarkdown"    "roxygen2"    
    ##  [91] "rprojroot"    "rstudioapi"   "rversions"    "rvest"        "scales"      
    ##  [96] "selectr"      "sessioninfo"  "shiny"        "sourcetools"  "stringi"     
    ## [101] "stringr"      "sys"          "testthat"     "tibble"       "tidyr"       
    ## [106] "tidyselect"   "tidyverse"    "tinytex"      "translations" "usethis"     
    ## [111] "utf8"         "vctrs"        "viridisLite"  "whisker"      "withr"       
    ## [116] "xfun"         "xml2"         "xopen"        "xtable"       "yaml"

``` r
## study package naming style (all lower case, contains '.', etc

## use `fields` argument to installed.packages() to get more info and use it!
ipt2 <- installed.packages(fields = "URL") %>%
  as_tibble()
ipt2 %>%
  mutate(github = grepl("github", URL)) %>%
  count(github) %>%
  mutate(prop = n / sum(n))
```

    ## # A tibble: 2 x 3
    ##   github     n  prop
    ##   <lgl>  <int> <dbl>
    ## 1 FALSE     44 0.297
    ## 2 TRUE     104 0.703
