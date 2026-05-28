---
tags: [matematica, notazione-asintotica, ricorrenze, analisi]
data_creazione: 2026-05-28
data_aggiornamento: 2026-05-28
fonti:
  - raw/Richiami matematici_con_integrazioni.pdf
---

# Richiami Matematici

Strumenti matematici fondamentali per l'analisi degli algoritmi. Il nucleo è la **notazione asintotica**, che descrive il comportamento di T(n) per n → +∞ ignorando costanti e termini di ordine inferiore. Completano il quadro le proprietà delle notazioni e i metodi per risolvere le equazioni di ricorrenza.

## Punti chiave

- Cinque notazioni asintotiche: Θ (stretto), O (superiore), Ω (inferiore), o (superiore non stretto), ω (inferiore non stretto)
- **Teorema fondamentale**: f(n) = Θ(g(n)) ⟺ f(n) = O(g(n)) ∧ f(n) = Ω(g(n))
- Le notazioni godono di transitività, riflessività (Θ, O, Ω), simmetria (solo Θ), simmetria trasposta (O↔Ω, o↔ω)
- O e Ω del caso peggiore si propagano a tutti gli input: T^p(n) = O(g(n)) → T(n) = O(g(n)) per qualunque input
- Tre metodi per risolvere ricorrenze: sostituzione, iterativo (albero della ricorsione), teorema principale (Master)
- L'omissione di funzioni base/tetto e condizioni al contorno non altera il comportamento asintotico

## Notazione Θ — limite asintotico stretto

**Θ(g(n))** = { f(n) | ∃ c₁>0, c₂>0, n₀>0 : ∀ n ≥ n₀,  0 ≤ c₁·g(n) ≤ f(n) ≤ c₂·g(n) }

g(n) è un **limite asintotico stretto** per tutte le f(n) ∈ Θ(g(n)).

Intuizione: i termini di ordine basso sono trascurabili per n grande; c₁ si sceglie leggermente sotto il coefficiente del termine dominante, c₂ leggermente sopra.

Esempio: 3n³ – 2n² = Θ(n³), poiché ∃ n₀ t.c. ∀ n ≥ n₀: 2n³ ≤ 3n³ – 2n² ≤ 4n³.

Funzioni costanti: Θ(n⁰) = Θ(1) (notazione impropria ma diffusa).

## Notazione O — limite asintotico superiore

**O(g(n))** = { f(n) | ∃ c>0, n₀>0 : ∀ n ≥ n₀,  0 ≤ f(n) ≤ c·g(n) }

g(n) è un **limite asintotico superiore**. Θ(g(n)) ⊆ O(g(n)).

Uso pratico: T^p(n) = O(g(n)) → T(n) = O(g(n)) per qualunque input (il limite superiore del caso peggiore è limite superiore universale). Es. Insertion-Sort: T^p(n) = O(n²) → T(n) = O(n²).

**Attenzione**: T^p(n) = Θ(g(n)) ⟹ T(n) = O(g(n)), ma NON T(n) = Θ(g(n)), perché il caso migliore può avere ordine inferiore.

## Notazione Ω — limite asintotico inferiore

**Ω(g(n))** = { f(n) | ∃ c>0, n₀>0 : ∀ n ≥ n₀,  0 ≤ c·g(n) ≤ f(n) }

g(n) è un **limite asintotico inferiore**. Θ(g(n)) ⊆ Ω(g(n)).

T^best(n) = Ω(g(n)) → T(n) = Ω(g(n)) per qualunque input. Es. Insertion-Sort: T^best(n) = Ω(n) → T(n) = Ω(n).

Quindi T(n) di Insertion-Sort è compreso tra Ω(n) e O(n²).

## Notazione o — limite superiore non stretto

**o(g(n))** = { f(n) | ∀ c>0, ∃ n₀>0 : ∀ n ≥ n₀,  0 ≤ f(n) < c·g(n) }

f(n) = o(g(n)) ⟺ lim_{n→∞} f(n)/g(n) = 0.  
g(n) è un limite superiore non asintoticamente stretto. o(g(n)) ⊆ O(g(n)).

## Notazione ω — limite inferiore non stretto

**ω(g(n))** = { f(n) | ∀ c>0, ∃ n₀>0 : ∀ n ≥ n₀,  0 ≤ c·g(n) < f(n) }

f(n) = ω(g(n)) ⟺ lim_{n→∞} f(n)/g(n) = ∞.  
g(n) è un limite inferiore non asintoticamente stretto. ω(g(n)) ⊆ Ω(g(n)).

## Analogia con i numeri reali

| Notazione | Analogo reale |
|---|---|
| f(n) = O(g(n)) | a ≤ b |
| f(n) = Ω(g(n)) | a ≥ b |
| f(n) = Θ(g(n)) | a = b |
| f(n) = o(g(n)) | a < b |
| f(n) = ω(g(n)) | a > b |

Differenza: esistono funzioni asintoticamente non confrontabili (es. f(n) = n per n dispari / n² per n pari).

## Proprietà delle notazioni asintotiche

**Transitività**: vale per tutte e cinque le notazioni.  
**Riflessività**: f(n) = Θ(f(n)), f(n) = O(f(n)), f(n) = Ω(f(n)).  
**Simmetria**: f(n) = Θ(g(n)) ⟺ g(n) = Θ(f(n)).  
**Simmetria trasposta**: f(n) = O(g(n)) ⟺ g(n) = Ω(f(n));  f(n) = o(g(n)) ⟺ g(n) = ω(f(n)).

## Metodi per risolvere le ricorrenze

Una **ricorrenza** è un'equazione che descrive T(n) in termini di T su input più piccoli. È lo strumento naturale per analizzare algoritmi ricorsivi.

Convenzioni semplificate (non alterano il comportamento asintotico):
- Si omettono funzioni base e tetto: T(n) = 2T(n/2) + Θ(n) invece di T(2⌈n/2⌉) + Θ(⌊n⌋)
- Si assume T(n) = Θ(1) per n piccolo (condizioni al contorno implicite)

### Metodo di sostituzione
1. Si ipotizza la forma della soluzione (es. T(n) = O(n·lg n))
2. Si usa l'induzione matematica per determinare le costanti e verificare l'ipotesi

Euristiche: ricorrenze simili a note → soluzioni simili; provare prima limiti laschi poi stringerli.

### Metodo iterativo (albero della ricorsione)
Si sviluppa la ricorrenza come somma di termini dipendenti solo da n e dalle condizioni iniziali, poi si valutano le sommatorie.

Per T(n) = aT(n/b) + D(n) + C(n): l'albero ha profondità log_b(n), con aⁱ nodi al livello i, ciascuno di costo D(n/bⁱ) + C(n/bⁱ). Il totale è la somma su tutti i livelli.

Esempio: T(n) = 2T(n/2) + n² → somma geometrica decrescente → T(n) = Θ(n²).

### Metodo principale (Master Theorem)
Per ricorrenze della forma T(n) = aT(n/b) + f(n) con a≥1, b>1 — vedi [[ricorrenze]] per l'enunciato completo e le tre casistiche.

## Articoli correlati

- [[introduzione-algoritmi]]
- [[ricorrenze]]
- [[complessita-asintotica]]

## Fonti

- `raw/Richiami matematici_con_integrazioni.pdf` — Prof.ssa Marina Zanella, UniBS
