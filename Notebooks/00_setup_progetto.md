00 - Setup e Preparazione Progetto
================

``` r
# Chunk: installazione_pacchetti
# Elenco di tutti i pacchetti usati nel progetto, con una breve nota sul motivo.
# Vengono installati SOLO quelli mancanti: se rilanci questo chunk in futuro
# non reinstalla nulla che hai già.

pacchetti_progetto <- c(
  "tidyverse",   # dplyr, ggplot2, readr, tidyr, lubridate, forcats: pulizia dati e grafici
  "tsibble",     # struttura dati "tidy" per serie temporali (yearmonth, tsibble)
  "feasts",      # analisi esplorativa: ACF/PACF, gg_tsdisplay, decomposizione
  "fabletools",  # funzioni di supporto per i modelli fable (report, glance, accuracy)
  "fable",       # modelli di forecasting (ARIMA, ETS) integrati con tsibble
  "zoo",         # conversioni di date mensili (as.yearmon) e serie irregolari
  "urca",        # test di radice unitaria e cointegrazione (ADF, KPSS, Zivot-Andrews, Johansen)
  "tseries",     # test di stazionarietà alternativi e utility per serie temporali
  "vars",        # modelli VAR e selezione automatica dei ritardi (VARselect)
  "lmtest",      # test diagnostici sui modelli lineari (es. Breusch-Godfrey)
  "sandwich",    # NeweyWest(): errori standard robusti HAC (autocorr. + eterosch.)
  "FinTS",       # test ARCH-LM per l'eteroschedasticità condizionata sui residui
  "here"         # percorsi file robusti, ancorati alla cartella del progetto (.Rproj)
)

# Controlla quali pacchetti NON sono ancora installati sul sistema
pacchetti_mancanti <- pacchetti_progetto[
  !sapply(pacchetti_progetto, requireNamespace, quietly = TRUE)
]

# Installa solo quelli mancanti, forzando il tipo "binary" dove disponibile
if (length(pacchetti_mancanti) > 0) {
  install.packages(pacchetti_mancanti, type = "binary")
} else {
  cat("Tutti i pacchetti sono già installati.\n")
}
```

    ## Tutti i pacchetti sono già installati.

``` r
# Chunk: caricamento_librerie
# Caricamento di tutte le librerie necessarie al progetto, con nota sul loro uso.

library(tidyverse)   # dplyr, ggplot2, readr, tidyr: gestione dati e grafici
library(lubridate)   # manipolazione di date (incluso in tidyverse, richiamato esplicitamente)
library(tsibble)     # yearmonth, tsibble: rappresentazione tidy delle serie temporali
library(feasts)       # ACF, PACF, gg_tsdisplay: diagnostica esplorativa delle serie
library(fabletools)  # report(), glance(), accuracy(): supporto ai modelli fable
library(fable)       # ARIMA() e altri modelli di forecasting su tsibble
library(zoo)         # as.yearmon: conversioni di date mensili
library(urca)        # ur.df, ur.kpss, ur.za, ca.jo: test di radice unitaria e cointegrazione
library(tseries)     # test di stazionarietà e utility aggiuntive per serie storiche
library(vars)        # VAR(), VARselect(): modelli vettoriali autoregressivi
library(lmtest)      # bgtest(): test di autocorrelazione sui residui (Breusch-Godfrey)
library(sandwich)    # NeweyWest(): matrice di varianza robusta HAC (notebook 05-06)
library(FinTS)       # ArchTest(): test ARCH-LM per eteroschedasticità condizionata
library(here)        # here(): costruisce percorsi a partire dalla cartella del progetto
```

``` r
# Chunk: controllo_versioni
# Stampa un riepilogo di R e delle versioni dei pacchetti caricati.
# Utile per la riproducibilità: se qualcosa smette di funzionare in futuro,
# questo blocco aiuta a capire se è cambiata una versione di un pacchetto.
sessionInfo()
```

    ## R version 4.3.3 (2024-02-29 ucrt)
    ## Platform: x86_64-w64-mingw32/x64 (64-bit)
    ## Running under: Windows 11 x64 (build 26200)
    ## 
    ## Matrix products: default
    ## 
    ## 
    ## locale:
    ## [1] LC_COLLATE=Italian_Italy.utf8  LC_CTYPE=Italian_Italy.utf8   
    ## [3] LC_MONETARY=Italian_Italy.utf8 LC_NUMERIC=C                  
    ## [5] LC_TIME=Italian_Italy.utf8    
    ## 
    ## time zone: Europe/Rome
    ## tzcode source: internal
    ## 
    ## attached base packages:
    ## [1] stats     graphics  grDevices utils     datasets  methods   base     
    ## 
    ## other attached packages:
    ##  [1] here_1.0.1        FinTS_0.4-9       vars_1.6-1        lmtest_0.9-40    
    ##  [5] strucchange_1.5-4 sandwich_3.1-3    MASS_7.3-60.0.1   tseries_0.10-58  
    ##  [9] urca_1.3-4        zoo_1.8-13        fable_0.4.1       feasts_0.4.1     
    ## [13] fabletools_0.8.0  tsibble_1.2.0     lubridate_1.9.4   forcats_1.0.1    
    ## [17] stringr_1.5.1     dplyr_1.1.4       purrr_1.0.2       readr_2.1.5      
    ## [21] tidyr_1.3.1       tibble_3.2.1      ggplot2_3.5.2     tidyverse_2.0.0  
    ## 
    ## loaded via a namespace (and not attached):
    ##  [1] utf8_1.2.4           generics_0.1.4       anytime_0.3.11      
    ##  [4] stringi_1.8.3        lattice_0.22-5       hms_1.1.4           
    ##  [7] digest_0.6.35        magrittr_2.0.3       evaluate_0.23       
    ## [10] grid_4.3.3           timechange_0.3.0     fastmap_1.1.1       
    ## [13] rprojroot_2.0.4      fansi_1.0.6          scales_1.3.0        
    ## [16] cli_3.6.2            rlang_1.1.3          munsell_0.5.1       
    ## [19] withr_3.0.0          yaml_2.3.8           tools_4.3.3         
    ## [22] tzdb_0.5.0           colorspace_2.1-0     curl_5.2.1          
    ## [25] vctrs_0.6.5          R6_2.5.1             lifecycle_1.0.4     
    ## [28] pkgconfig_2.0.3      pillar_1.9.0         gtable_0.3.5        
    ## [31] glue_1.7.0           quantmod_0.4.29      Rcpp_1.0.14         
    ## [34] xfun_0.52            tidyselect_1.2.1     rstudioapi_0.16.0   
    ## [37] knitr_1.45           htmltools_0.5.8      nlme_3.1-164        
    ## [40] rmarkdown_2.26       xts_0.14.1           compiler_4.3.3      
    ## [43] quadprog_1.5-8       TTR_0.24.4           distributional_0.9.0
