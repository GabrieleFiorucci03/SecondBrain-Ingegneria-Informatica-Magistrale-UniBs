---
tags: [ricorrenze, analisi, divide-et-impera, master-theorem, metodo-sostituzione]
data_creazione: 2026-05-28
data_aggiornamento: 2026-05-28
fonti:
  - raw/Ricorrenze_con_integrazioni.pdf
  - raw/ricorrenze_addendum_1_25.pdf
---

# Ricorrenze

Un'**equazione di ricorrenza** descrive T(n) in termini di T su input più piccoli. È lo strumento naturale per analizzare la complessità degli algoritmi ricorsivi. La forma generale è: BASE T(piccolo) = costante; INDUZIONE T(n) = espressione che coinvolge T su valori minori.

## Punti chiave

- Il **procedimento standard** sostituisce O(f(n)) con c·f(n) (costante reale simbolica distinta per ogni termine)
- **Metodo 1 (per ipotesi)**: calcolare T per alcuni valori, ipotizzare la forma chiusa, dimostrare per induzione
- **Metodo 2 (sostituzioni successive)**: sviluppare la ricorrenza finché si arriva alla BASE, poi verificare per induzione
- **Metodo di sostituzione (tentativo)**: ipotizzare S(n): T(n) ≤ f(n), verificare BASE e INDUZIONE trovando i vincoli sulle costanti; se i vincoli ammettono soluzione, S(n) è vera
- **Teorema principale (Master)**: per T(n) = aT(n/b) + f(n) — tre casi in base al confronto tra f(n) e n^(log_b a)
- **Estensione del Master**: quando f(n) = Θ(n^(log_b a) · lg^k n), la soluzione è Θ(n^(log_b a) · lg^(k+1) n)

## Metodo 1: per ipotesi e induzione

**Procedimento**: calcolare T per n = 1, 2, 3, … → riconoscere il pattern → dimostrare per induzione il pattern riconosciuto.

**Esempio 1**: T(1) = a, T(n) = b + T(n–1)
- Calcolo: T(2) = b+a, T(3) = 2b+a, T(4) = 3b+a → ipotesi T(n) = a + (n–1)b
- Dimostrazione: BASE T(1) = a ✓; INDUZIONE T(n) = b + T(n–1) = b + a + (n–2)b = a + (n–1)b ✓
- **T(n) = O(n)**

**Esempio 2**: T(0) = a, T(1) = b, T(n) = c + T(n–2)
- Soluzione: T(n) = cn/2 + a per n pari; T(n) = c(n–1)/2 + b per n dispari
- **T(n) = O(n)**

**Esempio 3 (Merge-Sort)**: T(1) = a, T(n) = 2T(n/2) + bn  per n = 2^k
- Calcolo: T(1)=a, T(2)=2a+2b, T(4)=4a+8b, T(8)=8a+24b → ipotesi T(n) = na + bn·lg n
- **T(n) = O(n·lg n)**

**Esempio 4**: T(1) = a, T(n) = T(n–1) + b·g(n)
- Soluzione: T(n) = a + b · Σ_{j=2}^{n} g(j) = O(Σ g(j))

## Metodo 2: sostituzioni successive

Sviluppare la ricorrenza sostituendo ripetutamente finché si raggiunge la BASE:

**Esempio (Merge-Sort)**: T(n) = 2T(n/2) + bn → 4T(n/4) + 2bn → 8T(n/8) + 3bn → … → nT(1) + bn·lg₂n = na + bn·lg n

Si ipotizza un asserto S(i): T(n) = i·bn + 2^i·T(n/2^i) e si dimostra per induzione su i per 1 ≤ i ≤ log₂n. Per i = log₂n: T(n) = bn·log₂n + n·T(1) = bn·log₂n + na.

## Metodo di sostituzione (tentativo)

Si tenta di dimostrare S(n): T(n) ≤ f(n) per qualche f(n) con costanti da determinare.

**Struttura**: BASE → vincolo sulle costanti; INDUZIONE → altro vincolo. Se i vincoli sono compatibili, S(n) è vera e T(n) = O(f(n)).

**Esempio 5**: T(1) = a, T(n) = 2T(n/2) + bn
- Tentativo: S(n): T(n) ≤ c·n·log₂n + d
- BASE: a ≤ d → vincolo d ≥ a
- IND: sostituendo S(n/2) si ottiene T(n) ≤ cn·log₂n + n(b–c) + 2d ≤ cn·log₂n + d se n(b–c) + d ≤ 0
- Vincolo: d ≤ c–b → ammette soluzione con d = a, c = a+b → **T(n) = O(n·lg n)**

**Esempio 6**: T(1) = 3, T(n) = (2^(n/2) + 1)T(n/2)
- Tentativo I: S(n): T(n) ≤ c·2^n → vincoli contraddittori (c ≥ 3/2 e c ≤ 0) → fallisce
- Tentativo II: S(n): T(n) ≤ c·2^n + d → vincoli 3 ≤ 2c+d e c+d ≤ 0 → ammette soluzione (c=3, d=–3) → **T(n) = O(2^n)**

## Teorema principale (Master Theorem)

Per T(n) = aT(n/b) + f(n) con a ≥ 1, b > 1, si confronta f(n) con n^(log_b a):

| Caso | Condizione | Soluzione |
|------|-----------|-----------|
| 1 | f(n) = O(n^(log_b a – ε)) per ε > 0 (f polinomialmente più piccola) | T(n) = Θ(n^(log_b a)) |
| 2 | f(n) = Θ(n^(log_b a)) (stesso ordine) | T(n) = Θ(n^(log_b a) · lg n) |
| 3 | f(n) = Ω(n^(log_b a + ε)) per ε > 0 + regolarità: a·f(n/b) ≤ c·f(n) per c<1 | T(n) = Θ(f(n)) |

**Estensione caso 2**: se f(n) = Θ(n^(log_b a) · lg^k n) per k ≥ 0, allora T(n) = Θ(n^(log_b a) · lg^(k+1) n).

**Esempi applicati**:
- T(n) = 9T(n/3) + n: a=9, b=3, n^(log₃9)=n² > f(n)=n → **caso 1 → T(n) = Θ(n²)**
- T(n) = T(2n/3) + 1: a=1, b=3/2, n^(log_{3/2}1)=1 = f(n)=1 → **caso 2 → T(n) = Θ(lg n)**
- T(n) = 3T(n/4) + n·lg n: a=3, b=4, n^(log₄3) < f(n) + regolarità → **caso 3 → T(n) = Θ(n·lg n)**
- T(n) = 2T(n/2) + n·lg n: a=2, b=2, n^(log₂2)=n < f(n)=n·lg n ma NON polinomialmente → **Master non applicabile**; estensione con k=1 → **T(n) = Θ(n·lg²n)**
- T(n) = 27T(n/3) + Θ(n³/lg n): n^(log₃27)=n³ > f(n) ma non polinomialmente (rapporto lg n) → **né Master né estensione applicabili**

## Ricorrenze da Insertion-Sort

L'albero della ricorsione evidenzia visivamente la struttura delle ricorrenze:
- T(n) = O(1) + T(n–1): chain lineare, costo bn per ogni livello → T(n) = O(n)
- T(n) = O(n) + T(n–1): Insertion-Sort → costo livello i = b·i, somma aritmetica → T(n) = O(n²)
- T(n) = 2T(n/2) + O(n): Merge-Sort → ogni livello costa bn, log n livelli → T(n) = O(n·lg n)

## Articoli correlati

- [[richiami-matematici]]
- [[introduzione-algoritmi]]
- [[algoritmi-di-ordinamento]]
- [[quicksort]]

## Fonti

- `raw/Ricorrenze_con_integrazioni.pdf` — Prof.ssa Marina Zanella, UniBS
- `raw/ricorrenze_addendum_1_25.pdf` — integrazioni manoscritte (alberi ricorsione, metodo sostituzione)
