# I dati del progetto
 
Documentazione delle serie utilizzate nel progetto di previsione dell'inflazione
italiana: cosa misurano, da dove vengono, in quali unità, perché sono state
scelte e come sono state trattate.
 
---
 
## La domanda di ricerca, e perché determina la scelta dei dati
 
L'obiettivo è prevedere l'**inflazione italiana** e verificare se
l'informazione su altre grandezze economiche migliori la previsione rispetto a
un modello che guarda soltanto al passato dell'inflazione stessa.
 
Questa formulazione impone due requisiti ai dati:
 
1. serve una **variabile obiettivo** che rappresenti l'inflazione italiana in
   modo standard e non contestabile;
2. servono **variabili esplicative** scelte in base a un'ipotesi economica
   esplicita sul perché dovrebbero contenere informazione utile — non
   selezionate perché disponibili o perché correlate.
Le tre variabili esplicative rispondono a tre canali distinti attraverso cui
l'inflazione italiana può essere influenzata:
 
| Canale economico | Variabile | Ipotesi |
|---|---|---|
| Inflazione importata e politica monetaria comune | Inflazione area euro | L'Italia condivide moneta, tasso di policy e mercato unico: gli shock di prezzo comuni si trasmettono |
| Costi energetici | Prezzo del petrolio Brent | L'Italia è fortemente dipendente dalle importazioni energetiche: il costo dell'energia entra nei prezzi al consumo |
| Ciclo reale e pressione di domanda | Produzione industriale italiana | Un'economia che produce a pieno regime genera pressioni inflazionistiche dal lato della domanda |
 
---
 
## Variabile obiettivo: inflazione italiana
 
| | |
|---|---|
| **Nome nel codice** | `hicp_it` |
| **Fonte** | BCE Data Portal (data.ecb.europa.eu) |
| **Codice serie** | `HICP.M.IT.N.000000.4D0.ANR` |
| **Produttore del dato** | Eurostat, su dati Istat |
| **Unità di misura** | **Punti percentuali** (variazione % sui dodici mesi) |
| **Frequenza** | Mensile |
| **Copertura** | Gennaio 1997 – Agosto 2026 (356 osservazioni) |
| **Destagionalizzazione** | Nessuna (dato grezzo) |
| **File** | `Data/Raw/Inflazione_ita_jan1997_aug2026.csv` |
 
### Che cosa misura esattamente
 
L'**HICP** (*Harmonised Index of Consumer Prices*, in italiano IPCA) è l'indice
dei prezzi al consumo costruito secondo una metodologia **armonizzata** a
livello europeo. È lo strumento con cui Eurostat rende confrontabili le
inflazioni nazionali, e con cui si costruisce l'aggregato dell'area euro.
 
La serie usata non è l'indice ma il suo **tasso di variazione annuo**: ogni
osservazione indica di quanto i prezzi di quel mese sono cambiati rispetto allo
**stesso mese dell'anno precedente**. Un valore di 2.8 significa che ad agosto i
prezzi erano superiori del 2.8% rispetto ad agosto dell'anno prima.
 
Le componenti del codice serie, per chi volesse ritrovarla:
 
- `HICP` — dataset di riferimento
- `M` — frequenza mensile
- `IT` — Italia
- `N` — né destagionalizzato né corretto per i giorni lavorativi
- `000000` — indice complessivo, tutte le voci (*all-items*)
- `4D0.ANR` — tasso di variazione annuo
### Perché HICP e non gli indici nazionali italiani
 
L'Istat pubblica anche il **NIC** (per l'intera collettività nazionale) e il
**FOI** (per le famiglie di operai e impiegati, usato per le rivalutazioni
contrattuali). Il NIC è l'indice di riferimento per il dibattito pubblico
italiano, ma qui non sarebbe adeguato per una ragione dirimente: **l'aggregato
dell'area euro è costruito a partire dagli HICP nazionali**, non dai NIC. Usare
NIC per l'Italia e HICP per l'area euro significherebbe confrontare due
grandezze costruite con metodologie diverse, e il coefficiente di trasmissione
che si stimerebbe non avrebbe un'interpretazione pulita.
 
Le differenze fra HICP e NIC sono reali ma contenute: riguardano principalmente
il trattamento dei saldi stagionali, delle spese sanitarie a carico pubblico e
della composizione del paniere.
 
### Perché il tasso annuo e non l'indice o la variazione mensile
 
Tre ragioni convergenti:
 
- **è l'oggetto di interesse**: quando la BCE dichiara un obiettivo del 2%, o
  quando i giornali parlano di inflazione, si riferiscono a questa grandezza;
- **elimina la stagionalità di prim'ordine**: confrontando ogni mese con lo
  stesso mese dell'anno precedente, i saldi di gennaio o i rincari estivi non
  producono oscillazioni spurie (una stagionalità residua resta comunque, ed è
  proprio quella che i modelli individuano al ritardo 12);
- **è già una trasformazione stabilizzante**: l'indice dei prezzi è fortemente
  tendenziale e cresce quasi monotonamente, mentre il tasso di variazione
  oscilla attorno a un livello.
---
 
## Variabile esplicativa 1: inflazione dell'area euro
 
| | |
|---|---|
| **Nome nel codice** | `hicp_ea` |
| **Fonte** | BCE Data Portal |
| **Codice serie** | `HICP.M.U2.N.000000.4D0.ANR` |
| **Unità di misura** | **Punti percentuali** (variazione % sui dodici mesi) |
| **Frequenza** | Mensile |
| **Copertura** | Gennaio 1997 – Agosto 2026 (356 osservazioni) |
| **File** | `Data/Raw/Inflazione_eu_jan1997_aug2026.csv` |
 
Stessa metodologia, stessa unità e stesso dataset della serie italiana:
cambia solo il codice geografico, `U2` al posto di `IT`.
 
**La coerenza fra le due serie non è un dettaglio.** Le due variabili entrano
insieme nella relazione di cointegrazione, e il coefficiente stimato misura la
trasmissione dell'una sull'altra. Se provenissero da dataset o vintage diversi,
quel coefficiente incorporerebbe anche le differenze metodologiche fra le
fonti, e non sarebbe interpretabile. Per questa ragione entrambe sono state
scaricate dallo stesso dataset nello stesso momento.
 
### L'ipotesi economica
 
L'Italia condivide con gli altri paesi dell'area euro la moneta, il tasso di
policy della BCE e il mercato unico. Gli shock di prezzo che colpiscono l'intera
area — energia, materie prime, catene di fornitura, politica monetaria — si
trasmettono anche all'Italia. La domanda che il modello pone è **quanto**, e se
quella trasmissione sia stabile nel tempo.
 
### Una sottigliezza da dichiarare: la composizione variabile
 
Il codice `U2` indica l'area euro a **composizione variabile**: l'aggregato
comprende, in ogni momento, i paesi che a quella data facevano parte dell'unione
monetaria. Sono undici nel 1999 e venti oggi, con la Grecia dal 2001, la
Slovenia dal 2007, fino alla Croazia dal 2023.
 
È la convenzione standard della BCE e la serie più usata in letteratura, ma
significa che la variabile non descrive un insieme di paesi costante nel tempo.
L'alternativa — un aggregato a composizione fissa — avrebbe il problema opposto:
non rappresenterebbe l'area euro realmente esistente in ciascun periodo. Data la
dimensione economica dei paesi entrati dopo il 1999, l'impatto sull'aggregato è
comunque modesto.
 
---
 
## Variabile esplicativa 2: prezzo del petrolio Brent
 
| | |
|---|---|
| **Nome nel codice** | `brent` |
| **Fonte** | FRED, Federal Reserve Bank of St. Louis |
| **Codice serie** | `MCOILBRENTEU` |
| **Produttore del dato** | U.S. Energy Information Administration |
| **Unità originale** | **Dollari USA per barile** |
| **Unità nel progetto** | **Euro per barile** (dopo conversione) |
| **Frequenza** | Mensile (media dei prezzi giornalieri) |
| **Copertura** | Fino ad agosto 2026 |
| **File** | `Data/Raw/brent_crude_oil.csv.csv` |
 
### Perché il Brent e non il WTI
 
Esistono due prezzi di riferimento internazionali per il greggio: il **Brent**,
estratto nel Mare del Nord, e il **WTI** (*West Texas Intermediate*), americano.
Il Brent è il benchmark per il mercato europeo e asiatico, ed è quello che
determina il costo dell'energia importata in Europa. Il WTI riflette
prevalentemente le condizioni del mercato interno statunitense, e i due prezzi
possono divergere anche sensibilmente — come accadde fra il 2011 e il 2014, con
uno scarto che superò i venti dollari al barile.
 
Per una domanda sull'inflazione italiana il Brent è la scelta corretta.
 
### La conversione in euro, e perché è stata necessaria
 
Il Brent è quotato in dollari. Usarlo in quella forma introdurrebbe nel modello
un segnale estraneo: le variazioni del prezzo in dollari riflettono **sia** il
prezzo reale del petrolio **sia** le fluttuazioni del cambio USD/EUR, e queste
ultime non hanno un rapporto diretto con l'inflazione europea.
 
Convertendo in euro si isola il costo dell'energia effettivamente sostenuto
nell'area euro:
 
$$\text{EUR per barile} = \frac{\text{USD per barile}}{\text{USD per 1 EUR}}$$
 
### Il problema dell'euro che non esisteva, e la soluzione ECU
 
Il campione parte da gennaio 1997, ma l'euro nasce a gennaio 1999. Per i
ventiquattro mesi precedenti non esiste un cambio USD/EUR.
 
La soluzione adottata usa l'**ECU** (*European Currency Unit*), il predecessore
legale diretto dell'euro. Al momento dell'introduzione della nuova valuta il
tasso di conversione fu fissato a **1 ECU = 1 EUR esattamente**: non una stima
o un'approssimazione, ma una disposizione normativa.
 
La prova della continuità viene dalla fonte stessa: la Federal Reserve
pubblicava la serie "dollari USA per 1 ECU" fino a dicembre 1998, e da gennaio
1999 l'ha **sostituita** con "dollari USA per 1 euro". La stessa istituzione ha
trattato le due serie come un'unica serie continua, semplicemente rinominata.
 
Le due serie vengono quindi incollate senza sovrapposizioni né buchi, e il
notebook 01 include un controllo automatico (`stopifnot`) che verifica
l'assenza di date duplicate al confine, più un'ispezione visiva dei valori nei
mesi attorno a dicembre 1998.
 
### Le due serie del tasso di cambio
 
| | Prima parte | Seconda parte |
|---|---|---|
| **Nome nel codice** | `EXUSEC` | `EXUSEU` |
| **Descrizione** | Dollari USA per 1 ECU | Dollari USA per 1 Euro |
| **Fonte** | FRED (Board of Governors, release G.5) | FRED (Board of Governors, release G.5) |
| **Copertura usata** | fino a dicembre 1998 | da gennaio 1999 |
| **Unità** | USD per unità di valuta | USD per unità di valuta |
| **Definizione** | Media mensile dei *noon buying rates* certificati dalla Federal Reserve Bank of New York | idem |
| **File** | `Data/Raw/EXUSEC.csv` | `Data/Raw/EXUSEU.csv` |
 
### Un limite da dichiarare: prezzo nominale
 
Il Brent entra nel modello come **prezzo nominale**, non deflazionato. Un prezzo
di 70 euro al barile nel 1998 e nel 2026 rappresenta un costo reale diverso,
perché nel frattempo il livello generale dei prezzi è cambiato.
 
La scelta è comunque difendibile: deflazionare il prezzo del petrolio con
l'indice dei prezzi al consumo significherebbe introdurre la variabile obiettivo
dentro una variabile esplicativa, creando una circolarità. Inoltre il modello
usa il petrolio in **differenza logaritmica**, cioè in tasso di variazione, che
attenua il problema — su base mensile la variazione del livello generale dei
prezzi è di ordini di grandezza inferiore a quella del prezzo del greggio.
 
---
 
## Variabile esplicativa 3: produzione industriale italiana
 
| | |
|---|---|
| **Nome nel codice** | `ind_pro` |
| **Fonte** | FRED, Federal Reserve Bank of St. Louis |
| **Codice serie** | `ITAPROINDMISMEI` |
| **Produttore del dato** | OCSE, *Main Economic Indicators* |
| **Descrizione completa** | *Production, Sales, Work Started and Orders: Production Volume: Economic Activity: Industry (Except Construction) for Italy* |
| **Unità di misura** | **Numero indice, base 2015 = 100** |
| **Frequenza** | Mensile |
| **Copertura** | Gennaio 1955 – **Marzo 2024** |
| **File** | `Data/Raw/industrial_production_italy.csv.csv` |
 
### Che cosa misura
 
È l'indice del **volume** della produzione industriale italiana, escluse le
costruzioni. Misura quantità prodotte, non valore: non è quindi influenzato
dalle variazioni di prezzo, il che lo rende adatto a rappresentare il ciclo
reale. Un valore di 100 corrisponde al livello medio del 2015.
 
### L'ipotesi economica
 
È l'unica delle tre variabili esplicative che rappresenta il **lato della
domanda interna**. La logica è quella della curva di Phillips: un'economia che
opera vicino alla piena capacità genera pressioni sui prezzi, mentre
un'economia in recessione le riduce. La produzione industriale è un indicatore
mensile del grado di utilizzo della capacità produttiva — il PIL, che sarebbe
più completo, è disponibile solo trimestralmente e con ritardi maggiori.
 
### Il problema: la serie è ferma a marzo 2024
 
La serie FRED appartiene alla famiglia dei *Main Economic Indicators* dell'OCSE,
in larga parte **dismessa nel 2024**. La pagina FRED non indica alcuna data di
rilascio futura: quei dati non esistono più a valle di marzo 2024, e
riscaricare la serie non cambierebbe nulla.
 
Questo ha imposto una scelta esplicita nel notebook 01. Applicando un filtro
indiscriminato sui valori mancanti, l'intero dataset si sarebbe fermato a marzo
2024, sacrificando quasi due anni e mezzo di dati disponibili su inflazione e
petrolio — e riducendo il periodo di valutazione fuori campione da 44 a 15
osservazioni.
 
La soluzione adottata distingue fra variabili **essenziali** e di **supporto**:
 
- `hicp_it`, `hicp_ea` e `brent` sono essenziali. Sono la variabile obiettivo e
  le due che compongono la relazione di lungo periodo: senza una di esse non si
  stima né si prevede nulla. Una riga che ne abbia una mancante viene eliminata.
- `ind_pro` è di supporto. Entra nell'analisi di stazionarietà ed è disponibile
  come possibile regressore di breve periodo, ma non entra nella relazione di
  cointegrazione. Può quindi restare mancante nella coda del campione senza
  danno.
Il training set termina a dicembre 2022, periodo in cui `ind_pro` è interamente
disponibile: nessuna stima ne risente.
 
### Perché è finita esclusa dal modello
 
Due ragioni successive, entrambe emerse dall'analisi:
 
1. il test di Zivot-Andrews (notebook 02) ha stabilito che `ind_pro` è
   **stazionaria con un break strutturale** a giugno 2008 — la crisi
   finanziaria — mentre le altre tre serie sono I(1). Non condividendo lo stesso
   ordine di integrazione, non poteva entrare nel test di cointegrazione;
2. nella selezione della specificazione ECM (notebook 05), la sua versione
   differenziata non ha migliorato in modo apprezzabile nessun modello, e non
   compare nelle specificazioni finali.
Resta comunque parte integrante del lavoro: è l'unica variabile che ha richiesto
il test di Zivot-Andrews per essere classificata correttamente, ed è il caso che
dimostra perché quel test fosse necessario.
 
---
 
## Il campione: perché comincia nel 1997 e finisce nel 2026
 
**Inizio — gennaio 1997.** Non è una scelta ma un vincolo: l'HICP armonizzato
viene calcolato a partire dal 1996-1997, perché prima di allora non esisteva la
metodologia comune europea. È la prima data per cui l'inflazione italiana e
quella dell'area euro sono confrontabili.
 
**Fine — agosto 2026.** È l'ultimo mese disponibile per l'HICP al momento dello
scaricamento. Il Brent arriva alla stessa data.
 
**Dimensione complessiva: 356 osservazioni mensili**, pari a poco meno di
trent'anni.
 
### La separazione fra stima e valutazione
 
| | Periodo | Osservazioni |
|---|---|---|
| **Training set** | Gennaio 1997 – Dicembre 2022 | 312 (87.6%) |
| **Test set** | Gennaio 2023 – Agosto 2026 | 44 (12.4%) |
 
La soglia di dicembre 2022 è stata fissata **prima di vedere qualsiasi
risultato**, ed è rimasta invariata per tutto il progetto. È una condizione di
validità dell'esercizio: spostare la soglia dopo aver osservato le performance
significherebbe scegliere il periodo di valutazione in funzione dei risultati,
una pratica (*data snooping*) che invaliderebbe il confronto.
 
La scelta ha anche un senso sostanziale: il training set si chiude esattamente
al picco inflazionistico, e il test set copre l'intera fase di disinflazione
successiva. È un banco di prova severo, perché chiede ai modelli di prevedere un
regime diverso da quello su cui sono stati stimati.
 
---
 
## Le trasformazioni applicate
 
Nessun modello usa le serie nella forma originale. Le trasformazioni derivano
dall'analisi di stazionarietà del notebook 02.
 
| Variabile | Trasformazione | Nome | Unità risultante |
|---|---|---|---|
| `hicp_it` | Differenza prima | `d_hicp_it` | Punti percentuali di variazione mensile del tasso |
| `hicp_ea` | Differenza prima | `d_hicp_ea` | idem |
| `brent` | Differenza logaritmica | `dlog_brent` | Tasso di variazione mensile (≈ variazione %) |
| `ind_pro` | Differenza logaritmica | `dlog_ind_pro` | idem |
| `brent` | Logaritmo (in livello) | `log_brent` | Usato nella relazione di cointegrazione |
 
### Perché differenza semplice per le inflazioni e logaritmica per le altre
 
Le due serie di inflazione **possono assumere valori negativi** — l'Italia ha
attraversato episodi di deflazione nel 2016 e nel 2020 — e il logaritmo di un
numero negativo non è definito. Si usa quindi la differenza semplice.
 
Brent e produzione industriale sono livelli sempre positivi, per cui il
logaritmo è definito e la differenza logaritmica ha il vantaggio di essere
direttamente interpretabile come tasso di variazione percentuale.
 
### Perché il logaritmo del Brent nella relazione di lungo periodo
 
Nel test di cointegrazione il Brent entra come `log(brent)` anziché in livello,
per tre ragioni:
 
- **interpretabilità**: il coefficiente diventa un'elasticità, cioè una
  variazione percentuale su variazione percentuale, invece di un impatto
  assoluto in euro. Un aumento di un euro al barile ha un significato molto
  diverso se il petrolio costa 20 euro o 140;
- **coerenza**: il livello di partenza di una differenza logaritmica è, per
  definizione, il logaritmo del livello;
- **l'ordine di integrazione si conserva**: il logaritmo è una trasformazione
  monotona, quindi una serie I(1) strettamente positiva resta I(1).
---
 
## Riepilogo delle unità di misura
 
Questa tabella è la risposta rapida alla domanda "cosa significa il numero che
sto guardando".
 
| Variabile | Unità | Esempio | Come si legge |
|---|---|---|---|
| `hicp_it`, `hicp_ea` | Punti percentuali | 2.8 | I prezzi sono superiori del 2.8% rispetto allo stesso mese dell'anno prima |
| `d_hicp_it`, `d_hicp_ea` | Punti percentuali | −0.4 | Il tasso di inflazione è sceso di 0.4 punti rispetto al mese scorso |
| `brent` | Euro per barile | 68.5 | Un barile di Brent costava 68.5 euro |
| `dlog_brent` | Tasso di variazione | 0.05 | Il prezzo è aumentato di circa il 5% rispetto al mese scorso |
| `ind_pro` | Indice, 2015 = 100 | 100.5 | La produzione è pari al livello medio del 2015 |
| `dlog_ind_pro` | Tasso di variazione | −0.02 | La produzione è calata di circa il 2% rispetto al mese scorso |
| `usd_per_eur` | Dollari per euro | 1.08 | Un euro valeva 1.08 dollari |
| `ECT` | Punti percentuali | −1.2 | L'inflazione italiana è 1.2 punti **sotto** il livello di equilibrio di lungo periodo |
 
---
 
## Limiti dei dati, dichiarati
 
**1. Previsione pseudo-real-time, non real-time.** L'esercizio di previsione
assume che al momento di prevedere il mese *t* siano noti tutti i valori del
mese *t−1*. Nella realtà l'HICP viene pubblicato con due o tre settimane di
ritardo. Il vincolo grava però **allo stesso modo su tutti i modelli**
confrontati, inclusi l'ARIMA e il random walk, quindi non distorce il confronto
fra di essi: rende solo un po' ottimistici i livelli assoluti di accuratezza.
 
**2. Dati nella versione definitiva, non nel vintage storico.** Si usano i
valori come sono oggi, non come apparivano all'epoca. Per l'HICP l'impatto è
trascurabile, perché l'indice non viene praticamente rivisto dopo la
pubblicazione — a differenza della produzione industriale, soggetta a revisioni
sostanziali, che non a caso non entra in nessun modello finale. Un esercizio
pienamente real-time richiederebbe una banca dati di vintage storici, come il
*Real Time Database* della BCE.
 
**3. Serie della produzione industriale interrotta.** La fonte OCSE ha
dismesso la serie a marzo 2024. Il progetto la mantiene per l'analisi di
stazionarietà ma non le permette di limitare il campione.
 
**4. Area euro a composizione variabile.** L'aggregato include i paesi via via
entrati nell'unione monetaria, quindi non descrive un insieme costante nel
tempo. È la convenzione standard, e l'impatto è modesto per la dimensione
economica dei paesi entrati dopo il 1999.
 
**5. Prezzo del petrolio nominale.** Non deflazionato, per evitare la
circolarità di usare la variabile obiettivo dentro una variabile esplicativa.
L'uso in differenza logaritmica attenua il problema.
 
**6. Migrazione del dataset BCE.** Le serie di inflazione provengono dal
dataset `HICP`, che ha sostituito il precedente `ICP` — non più aggiornato dopo
la migrazione. I codici sono cambiati (`ICP.M.IT.N.000000.4.ANR` è diventato
`HICP.M.IT.N.000000.4D0.ANR`) e con essi, marginalmente, alcuni valori storici.
Il progetto documenta un confronto fra i risultati ottenuti con le due versioni
dei dati: le conclusioni non cambiano, il che costituisce di per sé una verifica
di robustezza.
 
---
 
## Come riscaricare i dati
 
| Serie | Dove | Codice |
|---|---|---|
| Inflazione Italia | data.ecb.europa.eu | `HICP.M.IT.N.000000.4D0.ANR` |
| Inflazione area euro | data.ecb.europa.eu | `HICP.M.U2.N.000000.4D0.ANR` |
| Brent | fred.stlouisfed.org | `MCOILBRENTEU` |
| Produzione industriale | fred.stlouisfed.org | `ITAPROINDMISMEI` |
| Cambio ECU/USD | fred.stlouisfed.org | `EXUSEC` |
| Cambio EUR/USD | fred.stlouisfed.org | `EXUSEU` |
 
Su FRED: aprire la pagina della serie, usare il pulsante **Download** e scegliere
CSV. Su data.ecb.europa.eu: cercare il codice serie ed esportare in CSV.
 
Il notebook 01 contiene una funzione di lettura (`leggi_hicp_bce`) che riconosce
automaticamente il formato dei file BCE, adattandosi a eventuali cambiamenti
dell'export. Le serie FRED hanno invece un formato stabile e vengono lette
direttamente.
 
Tutte le fonti sono **pubbliche e gratuite**, senza registrazione né chiavi API:
chiunque può riprodurre il dataset da zero.
