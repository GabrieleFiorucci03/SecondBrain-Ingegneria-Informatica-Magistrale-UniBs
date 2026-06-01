---
tags: [machine-learning, valutazione, errore, accuratezza, statistica]
data_creazione: 2026-05-29
data_aggiornamento: 2026-05-29
fonti:
  - raw/Evaluation-aprile-2021-web.pdf
---

# Valutazione delle Ipotesi e degli Algoritmi di Classificazione

La valutazione misura quanto bene un'ipotesi (modello) generalizza su dati non visti. È fondamentale distinguere errore sul training set (ottimistico) da errore sulla distribuzione reale (realistico).

## Punti chiave

- **True error** (errore vero): probabilità che h classifichi erroneamente un'istanza casuale della distribuzione D.
- **Sample error** (errore campionario): frazione di errori su un campione specifico S — stima del true error.
- `accuracy_S(h) ≡ 1 − error_S(h)`.
- Tre domande fondamentali: (1) Quanto bene stima il sample error il true error? (2) Se h batte h' su un campione, quanto è probabile che h sia migliore in generale? (3) Come usare dati limitati sia per learning sia per stima dell'accuratezza?

## Definizioni di errore

**True error** di h rispetto a target f e distribuzione D:
```
error_D(h) ≡ Pr_{x~D}[f(x) ≠ h(x)]
```

**Sample error** di h rispetto a f e campione S:
```
error_S(h) ≡ (1/n) · Σ_{x∈S} δ(f(x) ≠ h(x))
```

dove δ(f(x) ≠ h(x)) = 1 se h classifica male x, 0 altrimenti.

## Sample Error è uno stimatore non distorto

errors_S(h) si comporta come una **variabile aleatoria binomiale** (r errori su n esempi):

```
P(X = r) = C(n,r) · pʳ · (1-p)^(n-r)
E[X] = np;  Var(X) = np(1-p)
```

dove p = errorD(h). Quindi: `E[errors_S(h)] = E[r/n] = p = errorD(h)`.

errors_S(h) è uno **stimatore non distorto** di errorD(h).

**Problema**: se S è lo stesso training set usato per costruire h, errors_S(h) è **ottimisticamente distorto** (può essere < errorD(h)).  
**Soluzione**: h e S devono essere scelti **indipendentemente**.

## Confidence Intervals (Intervalli di Confidenza)

Se S contiene n esempi indipendenti (n ≥ 30):

```
errors_S(h) ± z_N · √(errors_S(h)(1 - errors_S(h)) / n)
```

Valori di z_N per diversi livelli di confidenza N%:

| N% | 50% | 68% | 80% | 90% | 95% | 98% | 99% |
|----|-----|-----|-----|-----|-----|-----|-----|
| z_N | 0.67 | 1.00 | 1.28 | 1.64 | 1.96 | 2.33 | 2.58 |

**Regola pratica**: approssimazione valida se `n · errors_S(h)(1 - errors_S(h)) ≥ 5`.

**Derivazione**: σ_{errors_S} = √(p(1-p)/n) ≈ √(errors_S(h)(1-errors_S(h))/n). Sostituire p = errors_S(h) dà la formula CI.

## Metodi statistici per stimare l'accuratezza

Data l'accuratezza di h su un campione limitato, ci si chiede:
- Come stima l'accuratezza su ulteriori esempi?
- Intervallo di confidenza per l'errore osservato?

Questi temi coprono: confidence intervals, test statistici, cross-validation, confronto tra algoritmi di apprendimento, metriche di performance (precision, recall, F1, ROC).

## Confronto tra due ipotesi

Dato d = errorD(h1) - errorD(h2) (differenza di errore vero), lo stimatore è:
```
d̂ = errors_S1(h1) - errors_S2(h2)
```

d̂ è non distorto se h1, h2, S1, S2 sono indipendenti.

**Confidence interval per d (grandi n1, n2)**:
```
d̂ ± z_N · √(errors_S1(h1)(1-errors_S1(h1))/n1 + errors_S2(h2)(1-errors_S2(h2))/n2)
```

Se n scarso → si può usare S1 = S2 = S (stesso test set): il CI diventa più conservativo (più stretto in varianza ma tecnicamente corretto).

## K-Fold Cross Validation

Valutazione di un algoritmo di apprendimento L su dataset D:

```
1. Partiziona D in k sottoinsiemi disgiunti S1, S2, ..., Sk (|Si| > 30)
2. Per i = 1, ..., k:
   Ti ← D − Si  (training set)
   hᵢ ← L(Tᵢ)  (ipotesi output)
   δᵢ ← errors_Sᵢ(hᵢ)
3. Restituisci: error_{L,D} = (1/k) Σ δᵢ
```

`accuracy_{L,D} = 1 - error_{L,D}`

## Confronto tra algoritmi L_A e L_B

Obiettivo: stimare `E_{S⊂D}[errorD(L_A(S)) - errorD(L_B(S))]`

Usando K-Fold Cross Validation:

```
1. Partiziona D in k insiemi S1..Sk
2. Per i = 1..k:
   Ti ← D − Sᵢ
   hA ← L_A(Tᵢ);  hB ← L_B(Tᵢ)
   δᵢ ← errors_Sᵢ(hA) − errors_Sᵢ(hB)
3. Restituisci: δ̄ = (1/k) Σ δᵢ
```

Se δ̄ < 0 → stimiamo che L_A è migliore di L_B.

## Metodi di Valutazione Pratici

| Metodo | Descrizione |
|--------|------------|
| **Holdout** | 2/3 training, 1/3 test |
| **Random subsampling** | Holdout ripetuto più volte |
| **K-Fold Cross Validation** | k partizioni disgiunte, test a turno su ciascuna |
| **Leave-One-Out** | K-Fold con k = n (ogni esempio è test set da solo) |
| **Bootstrap** | Campionamento con rimpiazzo |

### Bootstrap

Scegliere n volte un esempio da un dataset di n esempi (con rimpiazzo): in media si ottiene il **63.2%** degli esempi distinti (poiché P(non scelto) = (1-1/n)ⁿ ≈ e⁻¹ ≈ 0.368).

```
Accuracy_boot = (1/b) Σᵢ₌₁ᵇ (0.632 × εᵢ + 0.368 × acc_s)
```

dove εᵢ = accuratezza del modello sul bootstrap training set i, acc_s = accuratezza su tutti gli esempi (full training set come test).

## Metriche di Performance nella Classificazione

### Confusion Matrix (2 classi)

|  | Predicted: Yes | Predicted: No |
|--|--|--|
| **True: Yes** | TP (True Positive) | FN (False Negative) |
| **True: No** | FP (False Positive) | TN (True Negative) |

Metriche derivate:
- **Error rate** = (FN + FP) / N
- **Accuracy** = 1 - Error rate = (TP + TN) / N
- **Recall / Sensitivity** = TP / (TP + FN) = veri positivi trovati su tutti i positivi reali
- **Specificity** = TN / (TN + FP)
- **Precision** = TP / (TP + FP)
- **False alarm rate** = FP / (FP + TN) = 1 - Specificity

### F1 Score

Media armonica tra precision e recall:
```
F1 = 2·precision·recall / (precision + recall) = 2TP / (2TP + FN + FP)
```

FN e FP hanno la stessa importanza.

### F2 Score

Media armonica pesata (β = 2): **enfatizza i falsi negativi**:
```
F₂ = 5·TP / (5·TP + 4·FN + FP)
```

**Macro F2 Score** per problemi multi-classe: media aritmetica degli F2 score di tutte le classi.

### ROC Curve (Receiver Operator Curve)

Plot di:
- Asse Y: True Positive rate (Sensitivity/Recall) = TP/(TP+FN)
- Asse X: False Positive rate (1 - Specificity) = FP/(FP+TN)

Generata variando la soglia di decisione dell'algoritmo.

Un classificatore perfetto → punto in alto a sinistra (1,0). Un classificatore random → diagonale. Meglio: curva la più a sinistra possibile (AUC vicino a 1).

### Confusion Matrix Multiclasse

Per k classi: matrice k×k dove la cella (i,j) conta le istanze di classe Cᵢ classificate come Cⱼ. La diagonale principale contiene le predizioni corrette; gli elementi fuori diagonale sono gli errori (con informazione su quali classi vengono confuse).

## Articoli correlati

- [[alberi-di-decisione]]
- [[overfitting-decision-trees]]
- [[bayesian-learning]]
- [[ensemble-methods]]

## Fonti

- raw/Evaluation-aprile-2021-web.pdf
