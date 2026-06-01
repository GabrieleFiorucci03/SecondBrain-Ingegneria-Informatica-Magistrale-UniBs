---
tags: [data-mining, processo, etica, pitfalls, feature-engineering, dati]
data_creazione: 2026-05-29
data_aggiornamento: 2026-05-29
fonti:
  - raw/MLDM-DataAnalysis.pdf
---

# Il Processo di Data Mining

Il data mining non è un'operazione lineare `Dati → Algoritmo → Risultato`, ma un ciclo iterativo che richiede competenza in raccolta dati, preprocessing, feature engineering, modellazione e valutazione. È una **scienza sperimentale**: non esiste un algoritmo universalmente migliore.

## Punti chiave

- Non c'è magia nel data mining: è un ampio array di tecniche alternative.
- Non esiste un metodo universale ottimale — è scienza sperimentale.
- Python rende tutto facile... forse troppo: capire cosa si sta facendo è essenziale.
- Ci sono molte **pitfalls** (trappole): è facile essere ingannati inconsapevolmente.
- Il focus deve essere su **valutazione e significatività statistica** dei risultati.
- Il data mining rivela **correlazione**, non causalità.

## Cos'è il dato

Un dataset è una collezione di **oggetti** e dei loro **attributi**.

- **Oggetto** (sinonimi: record, punto, caso, campione, entità, istanza): l'unità base.
- **Attributo** (sinonimi: variabile, campo, caratteristica, feature): una proprietà dell'oggetto.

### Tipi di attributo

| Tipo | Proprietà | Esempio |
|------|-----------|---------|
| **Nominale** | Valori discreti senza ordine | Colore occhi, stato civile |
| **Ordinale** | Valori con ordine ma distanze non significative | Taglia (S,M,L,XL) |
| **Intervallo** | Differenze significative, zero non assoluto | Temperatura in °C |
| **Ratio** | Zero assoluto, rapporti significativi | Peso, altezza, reddito |

## Feature Engineering

La fase più critica e creativa del data mining. Filtri comuni (implementati in WEKA e scikit-learn):

| Filtro | Scopo |
|--------|-------|
| **AddExpression** (MathExpression) | Applicare espressione matematica per creare/modificare attributi |
| **Center / Normalize / Standardize** | Media zero; range numerico; media zero + varianza unitaria |
| **Discretize** | Trasformare attributi numerici in nominali (anche supervisioned) |
| **PrincipalComponents** | Riduzione dimensionalità tramite PCA |
| **RemoveUseless** | Rimuovere attributi che non variano o variano troppo |
| **TimeSeriesDelta / Translate** | Differenze successive tra istanze consecutive |

## Pitfalls e Pratfalls

### Be skeptical — è facile ingannare (e ingannarsi)

- Per test affidabili: usare un campione **completamente fresco** non visto in nessuna fase di sviluppo.
- Data usata per sviluppo (in qualsiasi modo) è **"tainted"** (contaminata).

### Overfitting ha molte facce

- Non testare mai sul training set (ovvio).
- Qualsiasi dato usato per decisioni durante lo sviluppo è contaminato.
- Riservare dati di valutazione **solo per la fine**.

### Valori mancanti

"Missing" può significare: sconosciuto, non registrato, o irrilevante — **non è la stessa cosa**. La maggior parte degli algoritmi gestisce i missing values, ma con assunzioni diverse. La significatività stessa del valore mancante può essere informativa.

### No Free Lunch

Con 100 attributi binari: si conosce 1M di istanze su 2¹⁰⁰ ≈ 10³⁰ possibili. Il 99.9999...% dello spazio è ignoto.

**In order to generalize, every learner must embody some knowledge or assumptions beyond the data.**

Quindi: non esiste un algoritmo universalmente migliore. Il data mining è una scienza sperimentale: confrontare sempre più metodi sul proprio problema.

### Correlazione ≠ Causalità

Il data mining trova **correlazioni** non causazioni. Il gelato non causa annegamenti (entrambi correlati con l'estate). Per azioni, si vuole prevedere l'effetto delle proprie decisioni — serve pensiero causale beyond il data mining tradizionale.

## Data Mining ed Etica

### Privacy

Leggi europee sulla privacy:
- Ogni raccolta di dati personali deve avere uno scopo dichiarato.
- I dati devono essere eliminati quando non più necessari.
- Informazioni sensibili (orientamento sessuale, religione) non possono essere raccolte salvo circostanze estreme.

### Anonimizzazione

È più difficile di quanto sembri. Con **soli 3 attributi** (città, data di nascita, sesso):
- 50% degli americani è identificabile.
- 85% se si aggiunge il codice postale a 5 cifre.

Netflix (100M ratings): conoscendo 6 film e quando visti (±1 settimana) → identificazione del 99% degli utenti.

### Discriminazione

Il data mining è per definizione un sistema che discrimina (chi ottiene il prestito, chi ottiene l'offerta). Certe discriminazioni sono **illegali**: razza, sesso, religione. Attenzione: attributi apparentemente innocui (CAP, membership associazioni) possono correlare con attributi protetti.

## Articoli correlati

- [[introduzione-ml-dm]]
- [[valutazione-ipotesi]]
- [[overfitting-decision-trees]]
- [[alberi-di-decisione]]

## Fonti

- raw/MLDM-DataAnalysis.pdf
