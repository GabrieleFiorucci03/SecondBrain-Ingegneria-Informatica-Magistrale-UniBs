---
tags: [machine-learning, clustering, non-supervisionato, data-mining]
data_creazione: 2026-05-29
data_aggiornamento: 2026-05-29
fonti:
  - raw/MLDM-Cluster_analysis-1.pdf
---

# Cluster Analysis

La cluster analysis è un problema di apprendimento **non supervisionato** che consiste nel trovare gruppi di oggetti tali che gli oggetti dello stesso gruppo siano **simili tra loro** e **diversi** da quelli degli altri gruppi. Minimizza le distanze intra-cluster e massimizza le distanze inter-cluster.

## Punti chiave

- Apprendimento **non supervisionato**: non ci sono etichette di classe predefinite.
- Obiettivo: intra-cluster distances minimizzate, inter-cluster distances massimizzate.
- La nozione di cluster può essere **ambigua**: la stessa distribuzione di punti può essere interpretata come 2, 4, o 6 cluster a seconda della scala.
- **Non è** classificazione supervisionata, né semplice segmentazione, né risultato di una query.
- Applicazioni: grouping di documenti, proteine con funzionalità simile, azioni con fluttuazioni simili, riduzione di grandi dataset.

## Cosa non è Cluster Analysis

- **Classificazione supervisionata**: in quella si hanno etichette di classe già note.
- **Semplice segmentazione**: dividere studenti alfabeticamente per cognome non è clustering.
- **Risultati di una query**: i raggruppamenti da specifiche esterne non sono clustering.
- **Graph partitioning**: area correlata ma non identica.

## Applicazioni principali

- **Understanding**: raggruppare documenti per browsing, geni con funzionalità simile, azioni con andamenti simili.
- **Summarization**: ridurre la dimensione di grandi dataset.
- Geo-clustering: raggruppare zone geografiche per precipitazioni, temperature, ecc.

## Tipi di Clustering

### Partitional vs Hierarchical

- **Partitional**: divide i dati in sottoinsiemi **non sovrapposti** — ogni punto appartiene a esattamente un cluster.
- **Hierarchical**: produce un insieme di cluster **annidati** organizzati come albero (dendrogram). Può essere tradizionale (nested) o non-tradizionale.

### Tipi di cluster per definizione

| Tipo | Definizione |
|------|------------|
| **Well-separated** | Ogni punto è più vicino a qualsiasi altro punto del suo cluster che a qualsiasi punto fuori |
| **Center-based** | Ogni punto è più vicino al **centroide** del suo cluster che a quello di qualsiasi altro cluster |
| **Contiguous** (nearest-neighbor) | Un punto è più vicino ad almeno un altro punto del cluster che a qualunque punto esterno (transitivo) |
| **Density-based** | Regione densa di punti separata da regioni a bassa densità; adatto a cluster irregolari e con outlier |
| **Objective function** | Cluster che minimizzano/massimizzano una funzione obiettivo (es. SSE) — NP-hard in generale |

**Centroide**: media di tutti i punti del cluster.
**Medoide**: il punto "più rappresentativo" (esistente nel dataset).

## K-means Clustering

L'algoritmo K-means è il più noto approccio **partitional center-based**.

### Algoritmo base

```
1: Selezionare K punti come centroidi iniziali
2: repeat
3:   Assegnare ogni punto al cluster con centroide più vicino
4:   Ricalcolare il centroide di ogni cluster
5: until I centroidi non cambiano
```

### Dettagli

- I centroidi iniziali sono spesso scelti **casualmente** → risultati variano tra esecuzioni diverse.
- Closeness misurata con distanza Euclidea, cosine similarity, correlazione.
- Converge per misure di similarità comuni.
- La maggior parte della convergenza avviene nelle prime poche iterazioni.
- **Complessità**: O(n · K · I · d), dove n = punti, K = cluster, I = iterazioni, d = attributi.

### Valutazione: SSE (Sum of Squared Error)

```
SSE = Σᵢ₌₁ᴷ Σₓ∈Cᵢ dist²(mᵢ, x)
```

- mᵢ = centroide del cluster Cᵢ
- **Ottimizzazione**: minimizzare SSE rispetto ai centroidi → il centroide ottimale è la **media** dei punti del cluster (dimostrato analiticamente derivando SSE rispetto a mₖ).
- Se si usa distanza L₁ invece di Euclidea → il rappresentante ottimale è la **mediana**.

### Problemi con i centroidi iniziali

Se ci sono K cluster reali, la probabilità di scegliere casualmente un centroide per cluster è:
```
P = K! / K^K
```
Con K=10: P ≈ 0.00036 (molto bassa).

I centroidi iniziali a volte si riadattano da soli correttamente, a volte no → necessità di soluzioni come **K-means++** o esecuzioni multiple.

## Soluzioni al Problema dei Centroidi Iniziali

1. **Multiple runs**: eseguire K-means più volte con seed diversi, tenere il risultato con SSE minimo.
2. **Selezionare tra più di K centroidi**: generare molti centroidi candidati, scegliere i K più separati tra loro.
3. **Postprocessing**: eliminare cluster piccoli, splittare cluster con SSE alta, unire cluster vicini con SSE bassa.
4. **Bisecting K-means**: meno sensibile all'inizializzazione.

### Pre-processing e Post-processing

**Pre-processing**: normalizzare i dati, eliminare outlier.

**Post-processing**:
- Eliminare cluster piccoli (probabili outlier).
- Splittare cluster "loose" (SSE alta).
- Unire cluster "close" con SSE bassa.

## Bisecting K-means

Variante di K-means che può produrre clustering partitional o gerarchico. Meno suscettibile ai problemi di inizializzazione.

```
1. Inizializza: lista cluster = {cluster con tutti i punti}
2. Repeat:
   a. Seleziona un cluster dalla lista
   b. For i = 1 to number_of_iterations:
        Bisect il cluster usando K-means con K=2
   c. Aggiungi alla lista i 2 cluster con SSE minima
3. Until la lista contiene K cluster
```

## Limitazioni di K-means

K-means ha problemi con cluster di:
- **Dimensioni diverse**: il centroide del cluster grande "attira" punti del cluster piccolo
- **Densità diverse**: il cluster sparso assorbe punti dal cluster denso vicino
- **Forme non globulari**: K-means non riesce a separare cluster a forma di spirale o "banana"
- **Presenza di outlier**: singoli punti lontani spostano i centroidi

**Cluster vuoti**: se nessun punto viene assegnato a un centroide → soluzioni: scegliere il punto con SSE contribuzione massima, o il punto dal cluster con SSE più alta.

**Soluzione workaround**: usare molti più cluster di quanti ne esistano realmente → i cluster reali vengono partizionati, ma i frammenti sono vicini e possono essere ricomposti.

## K-means in Scikit-learn

```python
sklearn.cluster.KMeans(
    n_clusters=8,       # numero di cluster
    init='k-means++',   # metodo init: 'k-means++' o 'random'
    n_init=10,          # numero di run con seed diversi
    max_iter=300,       # max iterazioni per run
    tol=0.0001          # toleranza convergenza
)
```

- `init='k-means++'`: default, migliora la scelta iniziale dei centroidi
- `n_init=10`: K-means viene eseguito 10 volte, si restituisce il best (inertia minima)

## EM Algorithm (Expectation-Maximization)

Generalizzazione probabilistica di K-means. Invece di assegnare ogni punto a un singolo cluster, calcola la **probabilità di appartenenza** di ogni punto a ciascun cluster.

**Procedura iterativa**:
- **E step** (Expectation): calcolare la probabilità di cluster per ogni istanza.
- **M step** (Maximization): stimare i parametri della distribuzione dalle probabilità di cluster.

Le probabilità di cluster sono memorizzate come **pesi delle istanze**. Si ferma quando il miglioramento è trascurabile.

## DBSCAN (Density-Based Spatial Clustering of Applications with Noise)

Algoritmo di clustering **density-based**: trova cluster come regioni dense separate da regioni sparse. Supera i limiti di K-means su forme non globulari.

### Definizioni chiave

Parametri: **Eps** (raggio di neighborhood), **MinPts** (numero minimo di vicini).

- **Core point**: ha più di MinPts punti entro distanza Eps → è all'interno di un cluster.
- **Border point**: ha meno di MinPts punti entro Eps, ma è nel neighborhood di un core point.
- **Noise point**: non è né core né border point → viene eliminato.

### Algoritmo DBSCAN

```
1. Etichettare tutti i punti come core, border, o noise.
2. Eliminare i noise points.
3. Mettere un arco tra tutti i core points entro Eps l'uno dall'altro.
4. Ogni gruppo connesso di core points forma un cluster separato.
5. Assegnare ogni border point a uno dei cluster dei suoi core points associati.
   (se border point ha più core, scegliere arbitrariamente)
```

### Determinare Eps e MinPts

Idea: per punti in un cluster, il k-esimo nearest neighbor è a distanza simile. Per noise points, il k-esimo NN è molto più lontano.

**Metodo grafico**: plottare la distanza al k-esimo NN per ogni punto in ordine crescente → cercare il "ginocchio" della curva come soglia Eps.

### Quando DBSCAN funziona bene

- Cluster di **forme diverse** (spirali, anelli, contorni non convessi)
- Dati **rumorosi** (i noise points vengono identificati ed esclusi)
- Cluster di dimensioni diverse

### Quando DBSCAN NON funziona bene

- Dati con **densità molto variabile** tra cluster (nessun Eps vale per tutti)
- Dati **ad alta dimensionalità** (la nozione di densità degrada in molte dimensioni)

### DBSCAN vs K-means

| Caratteristica | K-means | DBSCAN |
|---------------|---------|--------|
| Forme cluster | Solo globulari | Qualsiasi forma |
| K da specificare | Sì | No |
| Gestione outlier | Sensibile | Identificati come noise |
| Densità variabile | OK | Problematico |
| High-dimensional | OK | Problematico |

### DBSCAN in Scikit-learn

```python
sklearn.cluster.DBSCAN(
    eps=0.5,          # raggio neighborhood (parametro più importante!)
    min_samples=5,    # MinPts (incluso il punto stesso)
    metric='euclidean'
)
```

## Cluster Validity

A differenza della classificazione supervisionata (dove esistono accuracy, precision, recall), nel clustering la valutazione è più ambigua — "clusters are in the eye of the beholder".

### Perché valutare

- Evitare di trovare pattern nel rumore (K-means trova cluster anche in dati random).
- Confrontare algoritmi di clustering.
- Confrontare due soluzioni di clustering.
- Determinare il numero "corretto" di cluster K.

### Aspetti della validazione

1. **Clustering tendency**: esiste struttura non-random nei dati? (prima di clusterizzare)
2. **Confronto esterno**: i cluster trovati corrispondono a classi esterne note?
3. **Goodness interno**: i cluster sono coesi e separati (senza info esterne)?
4. **Confronto relativo**: quale tra due clustering è migliore?
5. **Numero corretto di K**: qual è il K ottimale?

### Misure di Cluster Validity

| Tipo | Scopo | Esempio |
|------|-------|---------|
| **External Index** | Misurare corrispondenza con class labels esterni | Entropy, Purity |
| **Internal Index** | Misurare goodness senza info esterne | SSE, Silhouette |
| **Relative Index** | Confrontare due clustering | SSE o Entropy relativo |

### Internal: SSE per selezione di K

SSE decresce al crescere di K (più cluster → meno distanza intra-cluster). Plottare SSE vs K e cercare il **ginocchio** della curva → valore naturale di K. Se i cluster sono ben separati, il ginocchio è netto; se i dati sono rumorosi, la curva è monotona senza ginocchio.

### Internal: Cohesion e Separation

**Cluster Cohesion**: quanto sono vicini gli oggetti nello stesso cluster.
```
WSS (Within Sum of Squares) = Σᵢ Σ_{x∈Cᵢ} (x - mᵢ)²
```

**Cluster Separation**: quanto sono distinti i cluster tra loro.
```
BSS (Between Sum of Squares) = Σᵢ |Cᵢ|(m - mᵢ)²
```

dove m = centroide globale, mᵢ = centroide del cluster i.

**Proprietà fondamentale**: `BSS + WSS = costante` (varianza totale dei dati).  
K-means minimizza WSS ↔ massimizza BSS (non sempre si possono fare entrambe bene con K fisso).

### Internal: Silhouette Coefficient

Combina cohesion e separation per ogni singolo punto:

Per il punto i:
- **a** = distanza media di i a tutti gli altri punti del suo cluster (misura coesione)
- **b** = distanza media minima di i ai punti di un altro cluster (misura separazione)

```
s = 1 - a/b    se a < b (caso normale)
s = b/a - 1    se a ≥ b (punto misclassificato)
```

Range: [-1, 1]. Più vicino a 1 → meglio assegnato.  
Si può calcolare il **Silhouette medio** per un cluster o per l'intero clustering.

### External: Validazione tramite Similarity Matrix

Ordinare la similarity matrix per cluster label e ispezionare visivamente:
- **Cluster well-separated** → blocchi diagonali chiaramente rossi (alta similarità intra-cluster) e aree blu (bassa similarità inter-cluster).
- **Dati random** → matrice uniforme, nessun blocco → i "cluster" trovati non sono reali.

DBSCAN su dati ben strutturati produce blocchi più netti di K-means su dati non globulari.

### External: Entropy e Purity

Usate quando si ha un ground truth esterno (etichette di classe note).

**Entropy** del cluster i (grado di purezza):
```
eᵢ = -Σⱼ pᵢⱼ log₂ pᵢⱼ
pᵢⱼ = mᵢⱼ / mᵢ    (prob. che un membro del cluster i appartenga alla classe j)
Entropy totale = Σᵢ (mᵢ/m) · eᵢ
```

**Purity** del cluster i:
```
pᵢ = max_j pᵢⱼ
Purity totale = Σᵢ (mᵢ/m) · pᵢ
```

Esempio (LA Times, K=6 clustering di news articles): entropy totale = 1.1450, purity totale = 0.7203.

**Commento finale**: *"The validation of clustering structures is the most difficult and frustrating part of cluster analysis. Without a strong effort in this direction, cluster analysis will remain a black art accessible only to those true believers who have experience and great courage."* — Jain & Dubes, *Algorithms for Clustering Data*.

### Selezione delle variabili

La scelta delle **variabili su cui clusterizzare** è la decisione più critica:
- Anche una o due variabili irrilevanti possono distorcere il clustering.
- Le variabili devono descrivere la similarità rilevante per il problema.
- Scelta basata su: ricerca precedente, teoria, o ipotesi da testare.

## Articoli correlati

- [[introduzione-ml-dm]]
- [[support-vector-machines]]
- [[valutazione-ipotesi]]

## Fonti

- raw/MLDM-Cluster_analysis-1.pdf
