---
tags: [machine-learning, bayesiano, probabilità, naive-bayes, classificazione]
data_creazione: 2026-05-29
data_aggiornamento: 2026-05-29
fonti:
  - raw/BayesLearning-web.pdf
---

# Bayesian Learning

L'apprendimento Bayesiano usa la probabilità per quantificare l'incertezza sulle ipotesi. Gli esempi aggiornano la probabilità che un'ipotesi sia corretta. Fornisce sia algoritmi pratici sia uno **standard di riferimento** per valutare altri algoritmi di apprendimento.

## Punti chiave

- **Teorema di Bayes**: P(h|D) = P(D|h)·P(h) / P(D)
- **MAP** (Maximum A Posteriori): h_MAP = argmax P(D|h)·P(h) — ignora P(D) perché non dipende da h.
- **ML** (Maximum Likelihood): h_ML = argmax P(D|h) — equivale a MAP se si assume P(h) uniforme.
- I **consistent learner** (zero errori su training) producono ipotesi MAP se si assumono prior uniformi e dati noise-free.
- Il **Bayes Optimal Classifier** è il classificatore ottimale: nessun altro metodo con stesso spazio H e stessa prior può fare meglio in media.
- Il **Naive Bayes** assume indipendenza condizionale tra attributi: computazionalmente efficiente e praticamente molto efficace.

## Teorema di Bayes

```
P(h|D) = P(D|h) · P(h) / P(D)
```

- P(h) = probabilità a priori dell'ipotesi h
- P(D) = probabilità a priori dei dati di training D
- P(h|D) = probabilità a posteriori di h dopo aver osservato D
- P(D|h) = probabilità di osservare D dato che h è vera (likelihood)

## MAP e ML

**hMAP** = argmax_h P(h|D) = argmax_h P(D|h)·P(h)

**hML** = argmax_h P(D|h) (caso speciale: prior uniformi)

### Brute Force MAP Concept Learning

Con dati noise-free e prior uniformi su H:
- P(D|h) = 1 se h è consistente con D, 0 altrimenti
- P(h|D) = 1/|VS_H,D| per ogni h consistente con D

→ Ogni consistent learner (come FIND-S) produce una ipotesi MAP.

## Evoluzione delle probabilità posteriori

All'aumentare dei dati di training, la distribuzione P(h|D) si concentra progressivamente su poche ipotesi (il version space si restringe). Con molti esempi, P(h|D) → 1 per l'ipotesi vera (se presente in H).

## Bayes Optimal Classifier

Dato un nuovo esempio x, la classificazione ottimale non è necessariamente quella di h_MAP, ma quella che massimizza la probabilità ponderata su **tutte** le ipotesi:

```
v* = argmax_{vj ∈ V} Σ_{hi ∈ H} P(vj|hi) · P(hi|D)
```

**Proprietà**: è il classificatore ottimale — nessun altro classificatore con gli stessi H e prior può avere errore medio inferiore.

**Nota importante**: la classificazione ottimale può corrispondere a un'ipotesi che non esiste esplicitamente in H (perché è combinazione lineare di più ipotesi).

### Gibbs Classifier

Alternativa computazionalmente economica al Bayes Optimal:
1. Scegliere casualmente un'ipotesi h secondo P(h|D).
2. Usare h per classificare il nuovo esempio.

Con prior corrette e uniformi su H, l'errore atteso del Gibbs classifier è al massimo il **doppio** dell'errore del Bayes Optimal.

## Naive Bayes Classifier

Uno dei metodi di apprendimento pratico più efficaci (insieme a DT, NN, k-NN).

**Assunzione Naive Bayes**: gli attributi sono **condizionalmente indipendenti** dato il valore target:
```
P(a1, a2, ..., an | vj) = Π P(ai | vj)
```

**Classificatore**:
```
v_NB = argmax_{vj} P(vj|d) · Π_{i=1..n} P(ai|vj, d)
```

Le probabilità si stimano contando le frequenze nel training set:
- P(vj|d) = |{V = vj}| / |d|
- P(αk|vj, d) = |{A = αk, V = vj}| / |{V = vj}|

### Problema degli zeri

Se nessun esempio di training ha classe vj e attributo ai, allora P(ai|vj) = 0, rendendo l'intero prodotto 0.

**Soluzione — m-estimate (Bayesian estimate)**:
```
P̂(ai|vj) ← (nc + m·p) / (n + m)
```
dove:
- n = numero esempi con v = vj
- nc = numero esempi con v = vj e a = ai
- p = probabilità a priori (es. 1/|ai|)
- m = "equivalent sample size" (peso dato alla prior)

### Note sul Naive Bayes

- L'assunzione di indipendenza condizionale è spesso **violata** in pratica, ma il classificatore funziona comunque sorprendentemente bene.
- Non serve stimare P̂(vj|x, d) correttamente: basta che il ranking tra le classi sia preservato.
- Applicazioni di successo: diagnosi medica, classificazione di testi, spam detection.

## Applicazione: Classificazione di Testi

Target concept: `Interesting? : Document → V = {+, −}`

Ogni documento è rappresentato da un vettore di parole (bag of words). L'assunzione Naive Bayes aggiuntiva: la probabilità di una parola in una posizione è **indipendente dalla posizione** → si stima solo P(wk|vj).

**Algoritmo LEARN_NAIVE_BAYES_TEXT(Examples, V)**:
1. Costruire Vocabulary = tutte le parole distinte negli esempi.
2. Per ogni classe vj: raccogliere docs_j, concatenarli in Text_j, contare n = parole totali, stimare P(wk|vj) = (nk + 1) / (n + |Vocabulary|) (m-estimate con m = |Vocabulary|, p = 1/|Vocabulary|).

**Classificazione CLASSIFY_NAIVE_BAYES_TEXT(Doc)**:
```
v_NB = argmax_{vj} P(vj) · Π_{i ∈ positions(Doc)} P(wi|vj)
```
Le parole fuori dal Vocabulary vengono ignorate.

**Esempio — Twenty NewsGroups**:
- Task: classificare documenti in 20 newsgroup (|V| = 20)
- Training: 1000 documenti per classe
- Risultato Naive Bayes: **89% accuratezza** (random guessing ≈ 5%)
- Preprocessing: rimuovere le x parole più frequenti ("the", "a", "of") e le parole rarissime (non danno informazione classificatoria)

## Articoli correlati

- [[alberi-di-decisione]]
- [[valutazione-ipotesi]]
- [[concept-learning]]
- [[reti-neurali-artificiali]]

## Fonti

- raw/BayesLearning-web.pdf
