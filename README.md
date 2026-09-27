# Inflazione Italia: ECM vs ARIMA

Previsione dell'inflazione italiana (IPCA) con un modello a correzione d'errore che sfrutta la cointegrazione con l'inflazione dell'area euro e il prezzo del petrolio, confrontato con benchmark univariati su un holdout 2023–2026 e su 200 previsioni in validazione incrociata a finestra espansiva.

Progetto in R, gennaio 1997 – agosto 2026, dati mensili BCE e FRED.

---

## La domanda

L'informazione sull'inflazione dell'area euro e sul prezzo del petrolio, organizzata in una relazione di equilibrio di lungo periodo, migliora la previsione a un mese dell'inflazione italiana rispetto a un modello che guarda solo alla propria storia?

## Risultati in breve

- **Esiste una relazione di cointegrazione** fra inflazione italiana, inflazione dell'area euro e log del Brent in euro (Johansen, rango 1). Nel lungo periodo un punto di inflazione in più nell'area euro corrisponde a **1.34 punti** in Italia. Lo squilibrio si riassorbe per circa **22–26% al mese** (semivita 2.3–2.7 mesi), con valori quasi identici da tre stimatori diversi.
- **Su 200 previsioni fuori campione l'ECM ha l'errore più basso**: RMSE inferiore del **14%** rispetto al random walk e del **4.6%** rispetto al miglior ARIMA. **Il vantaggio non è statisticamente significativo** (Diebold-Mariano, p = 0.112).
- **Il vantaggio è concentrato negli shock.** Durante la pandemia l'ECM riduce l'errore del **22.6%** rispetto al random walk e dell'**11.6%** rispetto al miglior ARIMA; nel 2022 del 10.9% e del 6.5%. Nel decennio 2010–2019 tutti i modelli si equivalgono.
- **Dopo il 2022 l'ECM perde.** Nella disinflazione 2023–2026 prevede il 7.5% peggio dell'ARIMA parsimonioso e sovrastima sistematicamente: l'equilibrio stimato implica un'inflazione italiana del 2.3–2.6%, mentre quella osservata è rimasta fra l'1% e l'1.5%. È un indizio di cambiamento strutturale nella relazione Italia–area euro.
- **Un solo taglio train/test avrebbe dato la conclusione opposta.** Sul solo holdout 2023–2026 vince l'ARIMA parsimonioso; su 17 anni vince l'ECM. La classifica dipende dal periodo di valutazione.
- **I criteri informativi non hanno predetto la capacità previsiva.** L'ARIMA selezionato automaticamente vinceva in-sample di 35.6 punti di AICc e perde fuori campione; il problema era stato diagnosticato in anticipo (radici AR e MA quasi cancellate).

## Risultati della validazione incrociata

Finestra espansiva, 200 origini da dicembre 2009, previsione a un passo, tutti i parametri (incluso il vettore di cointegrazione) ristimati a ogni passo.

| Modello | RMSE | MAE | RMSE relativo al random walk |
|---|---|---|---|
| **ECM_A5** | **0.4858** | 0.3297 | **0.860** |
| ECM_A8 | 0.4903 | 0.3283 | 0.868 |
| ARIMA parsimonioso | 0.5091 | **0.3226** | 0.901 |
| ARIMA automatico | 0.5166 | 0.3318 | 0.914 |
| Random walk | 0.5650 | 0.3425 | 1.000 |

**Per regime macroeconomico (RMSE):**

| Regime | n | Miglior ECM | Miglior ARIMA | Random walk | Vincitore |
|---|---|---|---|---|---|
| 2010–2013 crisi del debito | 48 | 0.3448 | 0.3535 | **0.3406** | random walk (+1.2%) |
| 2014–2019 bassa inflazione | 72 | **0.2460** | 0.2478 | 0.2786 | pari fra modelli stimati |
| 2020–2021 pandemia | 24 | **0.4425** | 0.5006 | 0.5719 | **ECM** (−11.6% vs ARIMA) |
| 2022 shock energetico | 12 | **1.0104** | 1.0804 | 1.1343 | **ECM** (−6.5% vs ARIMA) |
| 2023–2026 disinflazione | 44 | 0.6742 | **0.6269** | 0.8174 | **ARIMA** (+7.5% per l'ECM) |

## Il modello

La relazione di lungo periodo stimata con Johansen sul campione 1997–2022:

$$hicp\_it \approx 1.3431 \cdot hicp\_ea - 0.2912 \cdot \log(brent) + 0.6248$$

L'ECM selezionato (A5) spiega la variazione mensile dell'inflazione italiana con il termine di correzione d'errore ritardato, un ritardo dell'inflazione italiana, un ritardo dell'inflazione europea e il ritardo stagionale a 12 mesi. Tutti i regressori sono ritardati, quindi il modello è utilizzabile per previsioni genuine.

Il ritardo a 12 mesi è decisivo: nella griglia di 12 specificazioni, **tutti** i modelli che lo includono superano il test di Breusch-Godfrey (p fra 0.106 e 0.146) e **tutti** quelli che non lo includono lo falliscono (p ≤ 0.0009). Il coefficiente stagionale stimato dall'ECM (−0.294) cade fra quelli dei due SARIMA (−0.255 e −0.346).

## Pipeline

Ogni notebook contiene codice, output e interpretazione. I file `.md` si leggono direttamente su GitHub, grafici inclusi.

| Notebook | Contenuto |
|---|---|
| [00 Setup](Notebooks/00_setup_progetto.md) | Installazione dei pacchetti (binari, senza Rtools) |
| [01 Preparazione dati](Notebooks/01_preparazione_dati.md) | Lettura, raccordo ECU→EUR, conversione del Brent in euro |
| [02 Stazionarietà](Notebooks/02_stazionarieta.md) | ADF e KPSS incrociati, Zivot-Andrews con break endogeno |
| [03 Cointegrazione](Notebooks/03_cointegrazione.md) | Test di Johansen, vettore di cointegrazione, pesi di aggiustamento |
| [04 Benchmark ARIMA](Notebooks/04_benchmark_arima.md) | Due SARIMA, diagnosi delle radici quasi cancellate, Ljung-Box, ARCH |
| [05 ECM](Notebooks/05_ecm.md) | Griglia di 12 specificazioni su campione comune, errori standard HAC |
| [06 Previsioni](Notebooks/06_previsioni_confronto.md) | Holdout 2023–2026, test di Diebold-Mariano, analisi del bias |
| [07 Validazione incrociata](Notebooks/07_cross_validation.md) | 200 previsioni a finestra espansiva, risultati per regime |

## Dati

| Variabile | Serie | Fonte |
|---|---|---|
| Inflazione Italia (IPCA, tasso annuo) | `HICP.M.IT.N.000000.4D0.ANR` | BCE Data Portal |
| Inflazione area euro (HICP, tasso annuo) | `HICP.M.U2.N.000000.4D0.ANR` | BCE Data Portal |
| Brent (USD/barile, convertito in EUR) | `MCOILBRENTEU` | FRED |
| Cambio USD per ECU (fino al 1998) / per EUR (dal 1999) | `EXUSEC`, `EXUSEU` | FRED |
| Produzione industriale Italia | `ITAPROINDMISMEI` | FRED (ferma a marzo 2024) |

Campione: gennaio 1997 – agosto 2026, 356 osservazioni. Training fino a dicembre 2022 (312), test gennaio 2023 – agosto 2026 (44).

Fonti, unità di misura, trasformazioni e scelte di costruzione sono documentate in [DATI.md](DATI.md).

## Metodologia in breve

- **Stazionarietà.** ADF e KPSS hanno ipotesi nulle opposte e vengono incrociati; Zivot-Andrews distingue una radice unitaria da una stazionarietà mascherata da un break. Le due inflazioni e il Brent risultano I(1); la produzione industriale è I(0) con un break a giugno 2008 ed è quindi esclusa dalla cointegrazione.
- **Confronto equo.** I modelli con regressori contemporanei (che assumono di conoscere l'inflazione europea dello stesso mese) sono stimati ma esclusi dal confronto previsivo, perché userebbero informazione non disponibile al momento della previsione.
- **Criteri informativi confrontabili.** Tutte le specificazioni ECM sono stimate sullo stesso campione di 299 osservazioni, altrimenti i modelli con più ritardi otterrebbero AIC e BIC artificialmente migliori.
- **Inferenza robusta.** Il test ARCH-LM rifiuta l'omoschedasticità (p ≈ 10⁻¹³), quindi i p-value riportati usano errori standard di Newey-West.
- **Nessun look-ahead nel vettore di cointegrazione.** In validazione incrociata il test di Johansen viene rifatto a ogni finestra.

## Come riprodurre

Richiede R (sviluppato con R 4.3.3) e RStudio.

1. Clona il repository e apri **`Inflazione-Italia.Rproj`**. Aprire il progetto è necessario: tutti i percorsi sono costruiti con il pacchetto `here` a partire dalla cartella del progetto.
2. Esegui i notebook **in ordine, da 00 a 07**. Ognuno salva in `Data/Processed/` i file letti dal successivo.
3. Il notebook 07 esegue 200 stime complete e richiede alcuni minuti. Il risultato viene salvato in `Data/Processed/risultati_cv.rds` e riutilizzato alle esecuzioni successive; per forzare il ricalcolo imposta `ricalcola_cv <- TRUE`.

I dati grezzi sono inclusi in `Data/Raw/`. Le cartelle `Data/Processed/` e `Output/` sono vuote nel repository e si riempiono eseguendo i notebook.

## Struttura

```
Inflazione-Italia/
├── README.md
├── DATI.md                  documentazione dei dati
├── Inflazione-Italia.Rproj
├── Data/
│   ├── Raw/                 dati originali scaricati
│   └── Processed/           generati dai notebook
├── Notebooks/               .Rmd (sorgente) e .md (versione leggibile)
└── Output/                  tabelle esportate in CSV
```

## Limiti

- **Potenza statistica.** Il vantaggio dell'ECM è reale ma concentrato in pochi mesi, quindi il differenziale di perdita ha varianza alta e il test di Diebold-Mariano non rifiuta nemmeno su 200 osservazioni.
- **Previsione pseudo-real-time.** Si assume che il dato del mese precedente sia disponibile al momento della previsione, ignorando il ritardo di pubblicazione. Il vincolo grava allo stesso modo su tutti i modelli, quindi non altera il confronto, ma rende ottimistici i livelli assoluti di accuratezza.
- **Ordini e specificazioni fissati.** Gli ordini ARIMA e le specificazioni ECM sono stati scelti su dati fino al 2022, che si sovrappongono al periodo di validazione incrociata. La stessa concessione vale per tutti i modelli.
- **Solo orizzonte a un mese.** Su orizzonti di 3–12 mesi la classifica potrebbe cambiare.
- **Possibile rottura strutturale dopo il 2022**, suggerita dal bias dell'ECM nel periodo recente ma non verificata con un test formale.
- **Inflazione I(1).** Poiché `hicp_it` è già un tasso di variazione, trattarlo come I(1) implica che il log dell'indice dei prezzi sia I(2). È una scelta comune nella letteratura sull'inflazione dell'area euro, ma va tenuta presente.

## Estensioni possibili

Test formale di rottura strutturale nella relazione di lungo periodo dopo il 2022; test di esogeneità debole per giustificare formalmente l'ECM a equazione singola; previsioni a orizzonti più lunghi; esercizio real-time su vintage storici (Real Time Database della BCE).

---

**Marco Durante**
