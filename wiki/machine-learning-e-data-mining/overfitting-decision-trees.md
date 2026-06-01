---
tags: [machine-learning, overfitting, pruning, alberi-di-decisione]
data_creazione: 2026-05-29
data_aggiornamento: 2026-05-29
fonti:
  - raw/4-Decision_Trees_Overfitting-nonotes.pdf
---

# Overfitting negli Alberi di Decisione

L'overfitting è il fenomeno per cui un modello si adatta troppo al training set, perdendo capacità di generalizzazione. Negli alberi di decisione si manifesta quando il training set è troppo piccolo o contiene errori (rumore).

## Punti chiave

- **Definizione formale**: h ∈ H overfits D se esiste h' ∈ H tale che `error_train(h) < error_train(h')` ma `error_D(h) > error_D(h')`.
- Due cause principali: dati di training **rumorosi** o **insufficienti** (pochi esempi rappresentativi).
- Al crescere del numero di nodi, l'accuratezza sul training sale, ma quella sul test scende dopo un certo punto.
- Soluzioni: **pre-pruning** (fermarsi prima della classificazione perfetta) o **post-pruning** (costruire l'albero completo e poi potare).
- Il **post-pruning** è in pratica più efficace.

## Effetto dei dati rumorosi

Considerare un training set con un esempio "noisy" classificato erroneamente: l'albero indotto aggiunge nodi extra per classificare correttamente quell'esempio sul training, ma questi nodi peggiorano la generalizzazione.

Esempio con PlayTennis: aggiungendo D15 = `<Sunny, Hot, Normal, Strong, PlayTennis=No>` (rumoroso), l'albero cambia struttura per accomodarlo.

## Avoiding Overfitting: strategie

Per determinare la **dimensione corretta dell'albero**:

1. **Validation set**: usare un insieme separato (distinto da training e test) per valutare l'utilità del pruning.
2. **Test statistico**: stimare l'accuratezza sull'intera distribuzione dei dati con un test statistico.
3. **Minimum Description Length (MDL)**: usare una misura esplicita di complessità e fermarsi quando la codifica è minima.

## Reduced-Error Pruning

Approccio (1): dati divisi in **training**, **validation** e **test** (3 insiemi distinti).

Algoritmo:
1. Costruire l'albero completo sul training set.
2. Ripetere finché il pruning non migliora più l'accuratezza sul validation set:
   - Valutare l'impatto sul validation set di potare ciascun nodo (sostituire il sotto-albero con la classificazione di maggioranza degli esempi associati).
   - Rimuovere il nodo la cui rimozione migliora di più l'accuratezza sul validation set.

**Proprietà**: produce la versione più piccola del sotto-albero più accurato.

**Limite**: funziona male quando il dataset è piccolo (usare esempi come validation riduce il training).

## Rule Post-Pruning (usato in C4.5)

Utile quando i dati sono limitati. Procedura:

1. Indurre l'albero permettendo overfitting.
2. Convertire l'albero in un **insieme equivalente di regole** (una per ogni foglia, seguendo il percorso dalla radice).
3. **Potare ogni regola indipendentemente**: rimuovere gli antecedenti la cui rimozione non peggiora l'accuratezza stimata.
4. Ordinare le regole finali per accuratezza stimata e usarle in quest'ordine.

**Esempio di conversione**: dall'albero con OutLook → Humidity → No/Yes si ottiene:
- R1: IF (Outlook=Sunny) ∧ (Humidity=High) THEN PlayTennis=No
- R2: IF (Outlook=Sunny) ∧ (Humidity=Normal) THEN PlayTennis=Yes
- ...

La stima dell'accuratezza usa un validation set o un **test statistico pessimistico** sull'intero insieme di esempi.

## Articoli correlati

- [[alberi-di-decisione]]
- [[valutazione-ipotesi]]
- [[ensemble-methods]]

## Fonti

- raw/4-Decision_Trees_Overfitting-nonotes.pdf
