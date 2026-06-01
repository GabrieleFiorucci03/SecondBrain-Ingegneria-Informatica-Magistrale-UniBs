---
tags: [machine-learning, ensemble, bagging, boosting, random-forest, classificazione]
data_creazione: 2026-05-29
data_aggiornamento: 2026-05-29
fonti:
  - raw/MLDM-Ensemblelearning.pdf
---

# Ensemble Methods

I metodi ensemble costruiscono un **insieme di classificatori** dal training data e predicono la classe di nuovi record aggregando le predizioni di più classificatori. L'idea di base è costruire diversi "esperti" e farli votare.

## Punti chiave

- **Vantaggio**: spesso migliorano significativamente le performance predittive rispetto a un singolo classificatore.
- **Svantaggio**: producono output difficile da interpretare (black-box).
- Schema generale: (1) creare più dataset D1..Dt, (2) costruire un classificatore Ci per ciascuno, (3) combinare con C*.
- Principali tecniche: **Bagging**, **Random Forests** (randomizzazione), **Boosting** (AdaBoost), **Stacking**.

## Schema generale

```
D (training originale)
  ↓ → D1 → C1 ↘
  ↓ → D2 → C2 →  C* (classificatore combinato)
  ↓ → Dt → Ct ↗
```

## Bagging (Bootstrap Aggregating)

- Crea più dataset tramite **campionamento con rimpiazzo** (bootstrap).
- Allena un classificatore su ciascun dataset.
- Classifica per **voto a maggioranza** (classificazione) o media (regressione).
- Riduce la **varianza** del modello.
- Bias-variance decomposition: errore = bias² + varianza + rumore irriducibile.

## Random Forests

- Variante del bagging per alberi di decisione.
- Ad ogni nodo, invece di cercare il miglior split su tutti gli attributi, seleziona un **sottoinsieme casuale** di attributi.
- Aggiunge randomizzazione sia nei dati (bootstrap) che nella scelta degli attributi.
- Molto robusto all'overfitting.

## Boosting (AdaBoost)

- Costruisce classificatori **sequenzialmente**: ogni nuovo classificatore si concentra sugli esempi classificati male dai precedenti.
- Assegna **pesi** agli esempi di training: gli esempi difficili (classificati male) ricevono peso maggiore.
- La predizione finale è una **combinazione pesata** di tutti i classificatori.
- AdaBoost (Adaptive Boosting) è l'algoritmo più noto.

## Stacking

- Allena un **meta-classificatore** sulle predizioni dei classificatori base.
- Il meta-classificatore impara come combinare le previsioni dei modelli base.
- Più flessibile del semplice voto a maggioranza.

## Perché funziona: prova matematica

Con 25 classificatori indipendenti con error rate ε = 0.35:

```
P(ensemble sbaglia) = Σᵢ₌₁₃²⁵ C(25,i) · εⁱ · (1-ε)^(25-i) = 0.06 << ε
```

Nota: in pratica i classificatori **non sono indipendenti** (stessi dati, stesso algoritmo). L'indipendenza è un'assunzione semplificatrice, ma il vantaggio empirico persiste.

## Bias-Variance Decomposition

Errore totale atteso = **bias² + varianza + Var(ε)**

- **Bias**: errore introdotto approssimando un problema complesso con un modello semplice (underfitting).
- **Varianza**: errore dovuto alla sensitività al particolare training set usato (overfitting).
- **Var(ε)**: rumore irriducibile.

Graficamente: al crescere della flessibilità del modello, bias decresce e varianza aumenta; il minimo di MSE è nel mezzo.

Gli **ensemble methods riducono la varianza** (non il bias) → particolarmente utili per modelli ad alta varianza come i DT profondi.

## Bagging: dettaglio algoritmo

**Bagging = Bootstrap Aggregating** (sampling with replacement = bootstrap).

**Model generation**:
```
n = numero di istanze nel training set
For t = 1..T:
  Sample n instances da training set WITH replacement
  Apply learning algorithm → store Cₜ
```

**Classification**:
```
For each model Cₜ: predict class
Return la classe predetta più spesso (majority vote = "hard voting")
```

**Pasting**: sampling WITHOUT replacement. Stesso schema, meno diversità.

**Proprietà chiave**: funziona meglio quando il learner è **instabile** (piccole variazioni nel training → grandi variazioni nel modello, es. DT profondi).

**Scikit-learn**: `BaggingClassifier(base_estimator=None, n_estimators=10, bootstrap=True, ...)`
- Se `base_estimator=None` → usa `DecisionTreeClassifier`.
- Se `bootstrap=False` → Pasting.

## Randomizzazione

Alternativa/complemento al bagging: randomizzare l'algoritmo anziché i dati.
- Alcuni algoritmi hanno già una componente casuale (es. pesi iniziali in NN).
- Per algoritmi greedy: scegliere random tra le N migliori opzioni invece della migliore (es. attribute selection nei DT).
- Combinabile con bagging → maggiore diversità.

## Hard Voting vs Soft Voting

- **Hard voting**: ogni classificatore vota la sua classe, si prende la classe con più voti.
- **Soft voting**: si fanno la media delle probabilità stimate da ogni classificatore → migliore quando i classificatori stimano probabilità calibrate.

## Articoli correlati

- [[alberi-di-decisione]]
- [[overfitting-decision-trees]]
- [[valutazione-ipotesi]]
- [[reti-neurali-artificiali]]

## Fonti

- raw/MLDM-Ensemblelearning.pdf
