---
tags: [machine-learning, classificazione, alberi-di-decisione, supervisionato]
data_creazione: 2026-05-29
data_aggiornamento: 2026-05-29
fonti:
  - raw/3-Basic_classification-Decision_Trees.pdf
  - raw/4-Decision_Trees_Overfitting-nonotes.pdf
---

# Alberi di Decisione

Gli alberi di decisione (Decision Trees) sono modelli di classificazione supervisionata che partono dal training set per indurre un albero usato poi per classificare nuovi esempi. Ogni nodo interno testa un attributo, ogni ramo corrisponde a un valore di quell'attributo, ogni foglia assegna una classe.

## Punti chiave

- Rappresentano una **disgiunzione di congiunzioni** sui valori degli attributi (DNF: Disjunctive Normal Form).
- Adatti quando: istanze descritte da coppie attributo-valore, output discreto, attributi binari/nominali, possibili errori o valori mancanti nel training set.
- L'induzione usa una strategia **greedy** che sceglie ad ogni nodo l'attributo test che ottimizza un certo criterio.
- La classificazione di un nuovo esempio percorre il ramo corrispondente ai valori degli attributi fino a una foglia.
- Principali algoritmi di induzione: **Hunt's Algorithm** (il più antico), **CART**, **ID3**, **C4.5**, **SLIQ**, **SPRINT**.

## Struttura dell'albero

- **Nodi interni**: test su un attributo (es. `Refund = Yes/No`)
- **Rami**: valori possibili dell'attributo testato
- **Foglie**: etichetta di classe (es. `Cheat = Yes/No`)

Esempio (cheat detection su dati fiscali):
```
Refund
├── Yes → NO (non bara)
└── No → MaritalStatus
         ├── Married → NO
         └── Single/Divorced → TaxableIncome
                               ├── < 80K → NO
                               └── > 80K → YES
```

## Applicare il modello a dati di test

Per classificare un'istanza: si parte dalla radice e si segue il ramo corrispondente al valore dell'attributo testato, finché si raggiunge una foglia.

Esempio: `(Refund=No, MaritalStatus=Married, TaxableIncome=80K)` → `Refund=No` → `MaritalStatus=Married` → foglia **NO**.

## Hunt's Algorithm

Procedura ricorsiva per costruire l'albero. Sia **D_t** l'insieme di training record che raggiungono il nodo `t`:

1. Se D_t contiene record della stessa classe y_t → `t` è una foglia con etichetta y_t.
2. Se D_t è vuoto → `t` è una foglia etichettata con la classe di maggioranza del nodo padre.
3. Se D_t contiene record di classi diverse → scegliere un attributo test, partizionare D_t nei sottoinsiemi corrispondenti, applicare ricorsivamente.

## Scelta del miglior split

La **tree induction** è greedy: ad ogni nodo si sceglie l'attributo test che massimizza la **riduzione di impurità**. Tre problemi: (1) come specificare la condizione di test, (2) come determinare il miglior split, (3) quando fermarsi.

### Tipi di attributo per il test

| Tipo | Multi-way split | Binary split |
|------|-----------------|--------------|
| **Nominale** | Un ramo per ogni valore | Partiziona valori in due sottoinsiemi |
| **Ordinale** | Un ramo per ogni valore (mantiene l'ordine) | Gruppi contigui (`{S,M}` vs `{L}`) — NO split non contigui |
| **Continuo** | Intervalli (es. `<10K`, `[10K,25K]`, `>25K`) | Soglia binaria: `A < v` oppure `A ≥ v` |

Per gli attributi continui il metodo efficiente: ordinare i valori, scansione lineare aggiornando la count matrix e il Gini index, scegliere la split position con Gini minimo.

### Misure di Impurità del Nodo

**Gain (da massimizzare)** = riduzione di impurità dopo lo split:
```
Δ = I(ParentNode) - Σᵢ (nᵢ/n) · I(i)
```
dove n = record nel nodo padre, nᵢ = record nel figlio i, I = misura di impurità.

**Best split**: quello con Δ massimo (equivalente a minimizzare l'impurità pesata dei figli).

#### GINI Index (usato in CART, SLIQ, SPRINT)

```
GINI(t) = 1 - Σⱼ [p(j|t)]²
```
- p(j|t) = frequenza relativa della classe j nel nodo t
- **Minimo = 0**: nodo puro (tutti stessa classe)
- **Massimo = 1 - 1/nc**: distribuzione uniforme tra nc classi (massima impurità)

Esempi (2 classi, 6 record):
- (0,6): Gini = 0.000
- (1,5): Gini = 0.278
- (2,4): Gini = 0.444
- (3,3): Gini = 0.500

GINI per uno split in k partizioni:
```
GINIsplit = Σᵢ (nᵢ/n) · GINI(i)
```

**Weighted GINI**: partizioni più grandi e pure sono preferite.

#### Entropy / Information Gain (usato in ID3, C4.5)

```
Entropy(t) = -Σⱼ p(j|t) · log₂ p(j|t)
```
Information Gain = Entropy(padre) - Σᵢ (nᵢ/n)·Entropy(i)

#### Misclassification Error

```
Error(t) = 1 - max_j p(j|t)
```

**Nota pratica**: tutte e tre le misure danno performance simili nella tree induction. La scelta dell'attributo è la stessa nella maggior parte dei casi.

#### Information Gain: dettaglio e problema

```
GAINsplit = Entropy(p) - Σᵢ (nᵢ/n) · Entropy(i)
```

Usato in ID3 e C4.5. Esempio PlayTennis (Entropy radice = 0.940):
- Gain(S, Outlook) = 0.246 ← **miglior attributo**
- Gain(S, Humidity) = 0.151
- Gain(S, Wind) = 0.048
- Gain(S, Temperature) = 0.029

**Problema**: Information Gain preferisce attributi con molti valori (es. StudentID crea molte partizioni piccole ma pure → overfitting). Soluzione: **Gain Ratio** (usato in C4.5):

```
GainRATIO = GAINsplit / SplitINFO
SplitINFO = -Σᵢ (nᵢ/n) · log(nᵢ/n)
```

SplitINFO misura l'entropia della partizione stessa; penalizza partizioni con alto numero di figli piccoli.

#### Misclassification Error

```
Error(t) = 1 - maxᵢ P(i|t)
```

Esempi: (0,6)→0.000, (1,5)→1/6, (2,4)→1/3. Meno sensibile al cambiamento di distribuzione rispetto a GINI e Entropy → preferire GINI o Entropy per lo splitting.

### Stopping Criteria

1. Tutti i record del nodo appartengono alla **stessa classe** → foglia.
2. Tutti i record hanno **stessi valori degli attributi** → impossibile splittare → foglia.
3. **Early termination**: percentuale di istanze omogeneamente classificate sopra una soglia, numero di record sotto una soglia, o criteri anti-overfitting.

## Algoritmo generale (TreeGrowth)

```
TreeGrowth(E, F):
  if stopping_cond(E, F) = true then
    leaf = createNode()
    leaf.label = argmaxᵢ p(i|t)   // classe di maggioranza
    return leaf
  else
    root = createNode()
    root.test_cond = find_best_split(E, F)
    V = {possibili outcome del test_cond}
    for each v ∈ V do
      Ev = {e ∈ E | root.test_cond(e) = v}
      child = TreeGrowth(Ev, F)
      aggiungi child come figlio di root con etichetta v
    return root
```

## Esempio applicativo: COVID-19

Le slide del corso mostrano alberi di decisione addestrati su dati di pazienti COVID-19 ospedalizzati per predire sopravvivenza/morte a 2, 4 e 6 giorni. Features chiave: età, D-dimero, LDH, percentuale linfociti, neutrofili/linfociti.

Risultato interessante: l'attributo di split alla radice è sempre `age ≤ 65.5`, a conferma che l'età è il fattore predittivo più importante.

## Algoritmi principali

| Algoritmo | Note |
|-----------|------|
| Hunt's Algorithm | Uno dei primi, base concettuale |
| CART | Classification and Regression Trees; usa Gini index |
| ID3 | Usa Information Gain (Entropy) |
| C4.5 | Estensione di ID3; gestisce valori mancanti, post-pruning a regole |
| SLIQ, SPRINT | Scalabili a grandi dataset |

## DT come ricerca nello spazio delle ipotesi

L'induzione di DT è una **hill-climbing search** nello spazio di tutti i possibili alberi (le funzioni discrete sugli attributi = power set di X):
- Lo spazio è **completo**: la funzione target è sicuramente rappresentabile.
- Produce una **singola ipotesi** (non il version space).
- Le scelte di split sono **statistiche** (Information Gain) → robusto al rumore.
- Usa **tutti gli esempi** a ogni passo (non incrementale).

## Inductive Bias nei DT

I DT **non sono** unbiased: hanno un **search bias** (non language bias).

| Tipo | Esempio | Bias |
|------|---------|------|
| **Language bias** | Candidate Elimination | Restringe H (solo congiunzioni) |
| **Search bias** | ID3, C4.5 | H completo, ricerca incompleta guidata da euristica |

**Bias nei DT**: preferenza per alberi **corti** e con attributi ad **alta riduzione di impurità** vicini alla radice.

## Vantaggi degli Alberi di Decisione

- **Nonparametric**: non richiede assunzioni sulla distribuzione dei dati.
- Poco costoso da costruire, funziona su **grandi training set**.
- Estremamente veloce a classificare nuove istanze.
- Facile da interpretare per alberi piccoli (leggibile dall'umano).
- Rappresentazione espressiva per funzioni **discrete**.
- Robusto al rumore e agli attributi ridondanti.

## Problemi Pratici nel DT Learning

1. **Attributi continui** → split binario con soglia (già trattato).
2. **Valori mancanti** → tre strategie:
   - Sostituire con il valore più comune nel nodo.
   - Sostituire con il valore più comune nella classe dell'esempio.
   - Assegnare probabilità pᵢ a ogni valore possibile e propagare il record frazionato ai figli (metodo C4.5).
3. **Attributi con costo** → sostituire Gain(S,A) con:
   - Tan & Schlimmer: `Gain²(S,A) / Cost(A)`
   - Nunez: `(2^Gain(S,A) - 1) / (Cost(A) + 1)^w`
4. **Underfitting/Overfitting** → già trattato nell'articolo [[overfitting-decision-trees]].

## Articoli correlati

- [[overfitting-decision-trees]]
- [[bayesian-learning]]
- [[ensemble-methods]]
- [[valutazione-ipotesi]]

## Fonti

- raw/3-Basic_classification-Decision_Trees.pdf
- raw/4-Decision_Trees_Overfitting-nonotes.pdf
