---
tags: [machine-learning, concept-learning, version-space, candidate-elimination, inductive-bias]
data_creazione: 2026-05-29
data_aggiornamento: 2026-05-29
fonti:
  - raw/2-Concept_Learning.pdf
---

# Concept Learning

Il Concept Learning è il problema di apprendere una funzione booleana da esempi positivi e negativi. L'obiettivo è trovare un'ipotesi h che corrisponda alla funzione target f su tutti gli esempi del training set e generalizzi bene sui nuovi esempi.

## Punti chiave

- Il **Version Space** VS_H,D è l'insieme di tutte le ipotesi in H consistenti con D.
- Il VS è rappresentato compattamente dalle sue boundary: **G** (frontiera più generale) e **S** (frontiera più specifica).
- Il **Candidate Elimination Algorithm** esplora il VS aggiornando G e S ad ogni esempio.
- Un **consistent learner** produce ipotesi MAP (con prior uniformi e dati noise-free).
- Il bias induttivo (scelta di H) determina cosa si può e non si può imparare.
- Un learner **unbiased** (H = power set di X) non generalizza: ogni istanza non vista è classificata positivamente dalla metà delle ipotesi nel VS.

## Version Space

**Definizione**: VS_H,D = {h ∈ H | h è consistente con tutti gli esempi in D}

Un'ipotesi h è consistente con D se classifica correttamente tutti gli esempi.

### Rappresentazione compatta

Il VS è rappresentato solo dalle sue boundary:

**G (General boundary)**: insieme delle ipotesi maximally generali consistenti con D.
```
G ≡ {g ∈ H | Consistent(g,D) ∧ (¬∃g' ∈ H)[(g' >_g g) ∧ Consistent(g',D)]}
```

**S (Specific boundary)**: insieme delle ipotesi maximally specifiche consistenti con D.
```
S ≡ {s ∈ H | Consistent(s,D) ∧ (¬∃s' ∈ H)[(s >_g s') ∧ Consistent(s',D)]}
```

**Version Space Representation Theorem**: ogni membro del VS si trova tra le boundary G e S:
```
VS_H,D = {h ∈ H | (∃s ∈ S)(∃g ∈ G)(g ≥_g h ≥_g s)}
```

## Candidate Elimination Algorithm

Mantiene G (frontiera generale) e S (frontiera specifica) e le aggiorna ad ogni esempio:

**Esempio positivo d** (generalizzazione di S):
- Rimuovere da G ogni ipotesi inconsistente con d.
- Per ogni s in S inconsistente con d:
  - Rimuovere s da S
  - Aggiungere a S le generalizzazioni minimali h di s tali che h sia consistente con d e qualche membro di G sia più generale di h
  - Rimuovere da S ogni ipotesi più generale di un'altra in S

**Esempio negativo d** (specializzazione di G):
- Rimuovere da S ogni ipotesi inconsistente con d.
- Per ogni g in G inconsistente con d:
  - Rimuovere g da G
  - Aggiungere a G le specializzazioni minimali h di g tali che h sia consistente con d e qualche membro di S sia più specifico di h
  - Rimuovere da G ogni ipotesi meno generale di un'altra in G

### Trace dell'algoritmo (esempio EnjoySport)

Attributi: Sky, AirTemp, Humidity, Wind, Water, Forecast

- S_0 = {<∅,∅,∅,∅,∅,∅>}, G_0 = {<?,?,?,?,?,?>}
- Dopo es. 1 (pos): S_1 = {<Sunny,Warm,Normal,Strong,Warm,Same>}
- Dopo es. 2 (pos): S_2 = {<Sunny,Warm,?,Strong,Warm,Same>}
- Dopo es. 3 (neg): G_3 espande: {<Sunny,?,?,?,?,?>, <?,Warm,?,?,?,?>, <?,?,?,?,?,Same>}
- Dopo es. 4 (pos): convergenza verso VS finale

**Final Version Space**:
- S = {<Sunny,Warm,?,Strong,?,?>}
- G = {<Sunny,?,?,?,?,?>, <?,Warm,?,?,?,?>}

## Classificazione con il Version Space

Data una nuova istanza x:
- Se **tutti i membri del VS** la classificano positiva → classificare come **positiva**
- Se **tutti i membri del VS** la classificano negativa → classificare come **negativa**
- Altrimenti → **incerto** (majority vote con confidence rate)

Note: FIND-S produrrebbe una risposta errata per casi incerti (classifica come negativa).

## Convergenza

Il Candidate Elimination converge all'ipotesi corretta se:
1. Non ci sono errori nei training examples.
2. Esiste un'ipotesi in H che descriva correttamente il target concept.

Il target è **esattamente appreso** quando S e G convergono a una singola identica ipotesi.

## On-line Learning e Query Strategy

**Optimal query strategy**: generare istanze che soddisfano esattamente metà delle ipotesi nel VS corrente → permette di trovare il target concept con ⌈log₂|VS|⌉ query.

## Inductive Bias

Lo spazio H scelto costituisce il **bias induttivo** del learner.

**Learner biased** (H = congiunzioni di attr.-valore):
- Non può rappresentare concetti disgiuntivi come "Sky=Sunny OR Sky=Cloudy".
- H ha 973 ipotesi per il dominio EnjoySport.

**Learner unbiased** (H' = power set di X, include disgiunzioni, congiunzioni, negazioni):
- |H'| = 2^96 ≈ 10^28 per lo stesso dominio.
- Non generalizza: ogni istanza non vista è classificata + dalla metà delle ipotesi → inutile.
- S = disgiunzione degli esempi positivi; G = negazione della disgiunzione dei negativi.

**Conclusione**: senza bias induttivo non è possibile generalizzare oltre i dati osservati. Il bias è necessario ma introduce rischi di sbagliare su concetti non rappresentabili da H.

## Tre Learner con Diverso Bias (in ordine crescente di bias)

1. **Rote learner**: memorizza gli esempi; classifica x come YES/NO solo se corrisponde esattamente a un esempio visto. Nessun bias → nessuna generalizzazione.
2. **Candidate-Elimination**: usa il vote del VS. Classifica solo quando tutti i membri del VS concordano.
3. **FIND-S**: produce sempre l'ipotesi massimamente specifica. Forte bias → classifica sempre, ma può sbagliare.

Bias più forte → più istanze non viste vengono classificate. Bias più debole → meno errori impropri ma più istanze "incerte".

Il bias induttivo del Candidate-Elimination Algorithm è: *il target concept c è incluso in H*.
Il bias induttivo di FIND-S è: *c è incluso in H* **e** *la soluzione è l'ipotesi maximally specific*.

## Punti di sintesi

1. Concept learning come ricerca nello spazio delle ipotesi.
2. Ordinamento generale-to-specifico delle ipotesi.
3. Version space: Candidate Elimination Algorithm.
4. Le boundary S e G caratterizzano l'incertezza del learner.
5. Inductive jumps (generalizzazione) sono possibili solo se il learner è biased.

## Articoli correlati

- [[bayesian-learning]]
- [[alberi-di-decisione]]
- [[overfitting-decision-trees]]
- [[valutazione-ipotesi]]

## Fonti

- raw/2-Concept_Learning.pdf
