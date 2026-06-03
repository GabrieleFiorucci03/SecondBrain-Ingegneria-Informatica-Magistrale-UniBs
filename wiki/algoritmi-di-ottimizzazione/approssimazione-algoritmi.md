---
tags: [ottimizzazione, approssimazione, euristiche, TSP, knapsack, vertex-cover, bin-packing]
data_creazione: 2026-06-02
data_aggiornamento: 2026-06-02
fonti:
  - raw/Approximation Algorithms_2020.pdf
---

# Algoritmi di Approssimazione

Un algoritmo di approssimazione garantisce una soluzione con gap massimo ε rispetto all'ottimo, in tempo polinomiale. Alternativa agli [[exact-algorithms]] quando la complessità esatta è proibitiva. Si applica a problemi NP-hard come TSP, Vertex Cover, Bin Packing e Knapsack.

## Punti chiave

- **ε-approssimazione**: H è ε-approssimato se `(zᴴ − z*)/z* ≤ ε` (minimizzazione) ovvero `zᴴ ≤ (1+ε)z*`.
- Per problemi di massimizzazione: `zᴴ ≥ (1−ε)z*`.
- Il **Tree Algorithm** per TSP (con triangle inequality) è 1-approssimato: `zᴴ ≤ 2z*`.
- Il **Greedy Algorithm** per Max Independent Set non ha bound garantito: worst case infinitamente peggio dell'ottimo.
- Il **Rounding Algorithm** per Minimum Vertex Cover è 1-approssimato: `zᴴ ≤ 2z*`.
- Il **Next Fit** per Bin Packing è 1-approssimato: `zᴴ < 2⌈A⌉`.
- L'**euristica C migliorata** (Greedy) per Knapsack è ½-approssimata: `zgreedy ≥ ½z*`.

## Definizione formale

Dato un problema di minimizzazione P, un algoritmo H è **ε-approssimato** se:
```
(zᴴ − z*) / z* ≤ ε     con ε ≥ 0
```
Equivalentemente: `zᴴ ≤ (1+ε)z*`

Per massimizzazione: `zᴴ ≥ (1−ε)z*`

## TSP — Tree Algorithm

**Assunzioni**: matrice distanze simmetrica + triangle inequality `cᵢₖ + cₖⱼ ≥ cᵢⱼ`.

**Algoritmo**:
1. Calcola il **Minimum Spanning Tree** T di G;
2. Costruisci un multigrafo euleriano duplicando ogni arco di T;
3. Trova un **ciclo euleriano** E sul multigrafo;
4. Costruisci il **ciclo hamiltoniano** H eliminando le ripetizioni da E (shortcutting).

**Teorema**: Il Tree Algorithm è 1-approssimato (ε = 1, zᴴ ≤ 2z*).

**Dimostrazione**:
- Per triangle inequality: `c(H) ≤ c(E)` (shortcutting non aumenta il costo)
- Per costruzione del multigrafo: `c(E) = 2c(T)`
- Quindi: `c(H) ≤ 2c(T)`
- Rimuovendo un arco da H* si ottiene uno spanning tree → `c(H*) ≥ c(T)`
- Combinando: `c(H)/c(H*) ≤ 2c(T)/c(T) = 2 = 1+ε`  →  **ε = 1** □

**Esempio worst case**: n nodi equidistanti disposti su una circonferenza. Il Tree Algorithm produce un ciclo con backtracking che tende a 4π mentre l'ottimo tende a 2π (ratio → 2).

## Maximum Independent Set — Greedy Algorithm

**Problema**: trovare il sottoinsieme I ⊆ V di massimo peso tale che nessuna coppia di nodi in I è adiacente.
```
max  Σᵢ wᵢxᵢ
     xᵢ + xⱼ ≤ 1    ∀(i,j) ∈ E
     xᵢ ∈ {0,1}
```
Assumendo wᵢ = 1 ∀i.

**Greedy Algorithm**:
1. Inizia con soluzione vuota;
2. Ordina i nodi per grado crescente;
3. Seleziona il nodo di grado minimo;
4. Rimuovi dalla lista tutti i nodi adiacenti al selezionato;
5. Seleziona il prossimo nodo nella lista; ripeti.

**Analisi worst case**: L'algoritmo **non ha bound garantito**; può essere infinitamente peggio dell'ottimo.

**Istanza worst case**: grafo bipartito con Iₜ = independent set di t nodi + Kₜ = clique di t nodi, con connessione completa tra Iₜ e Kₜ.
- Grado di nodo in Iₜ = t; grado nodo in Kₜ = (t-1)+t = 2t-1.
- Greedy seleziona prima nodo in Iₜ (grado minore), poi un nodo in Kₜ. Soluzione: zᴴ = 2.
- Ottimo = Iₜ intero: z* = t.
- Per t → ∞: zᴴ = 2, z* → ∞ → **ratio illimitato**.

**Morale**: l'algoritmo è di solito buono in pratica, ma il problema stesso è intrinsecamente difficile.

## Minimum Vertex Cover — Rounding Algorithm

**Problema**: trovare il sottoinsieme V' ⊆ V di costo minimo tale che ogni arco (i,j) ∈ E abbia almeno un endpoint in V'.
```
min  Σᵢ cᵢxᵢ
     xᵢ + xⱼ ≥ 1    ∀(i,j) ∈ E
     xᵢ ∈ {0,1}
```

**Rounding Algorithm**:
1. Calcola xᴸᴾ soluzione ottima della LP relaxation;
2. Arrotonda: `xᵢᴴ = 1 se xᵢᴸᴾ ≥ 0.5, else 0`.

**Attenzione**: arrotondare soluzioni LP è in generale **inaffidabile** (può generare soluzioni inammissibili). In questo caso specifico è sicuro.

**Fattibilità** (dimostrazione per contraddizione):
- Se xᴸᴾ è ammissibile → `xᵢᴸᴾ + xⱼᴸᴾ ≥ 1` per ogni arco.
- Se xᵢᴸᴾ < 0.5 E xⱼᴸᴾ < 0.5 → `xᵢᴸᴾ + xⱼᴸᴾ < 1` → contraddizione.
- Quindi almeno uno dei due endpoint è ≥ 0.5 → viene selezionato (xᴴ = 1). ✓

**Bound** (1-approssimazione):
- `zᴴ = Σᵢ∈V' cᵢ ≤ 2Σᵢ∈V cᵢxᵢᴸᴾ = 2zᴸᴾ ≤ 2z*`
- Worst case: tutti i variabili LP = 0.5 → zᴴ = 2zᴸᴾ

Quindi: `zᴴ ≤ (1+ε)z*` con **ε = 1** □

## Bin Packing — Next Fit Algorithm

**Problema**: n oggetti con pesi aᵢ ≤ 1, contenitori di capacità 1. Minimizzare il numero di contenitori usati.

**Next Fit Algorithm**:
1. Prendi il primo oggetto nella lista;
2. Inseriscilo nel contenitore corrente; se non entra, apri un **nuovo contenitore** e inseriscilo lì (il nuovo diventa il corrente);
3. Vai al prossimo oggetto e ripeti.

**Nota**: quando un oggetto non entra, si apre sempre un nuovo contenitore senza controllare i precedenti.

**Bound** (1-approssimazione): sia A = Σᵢ aᵢ (somma totale pesi), zᴴ = numero bin di Next Fit.
1. `z* ≥ ⌈A⌉` (lower bound banale)
2. In ogni coppia di bin consecutivi, la somma dei pesi è > 1 (altrimenti si sarebbero combinati)
3. Quindi: `zᴴ < 2⌈A⌉`
4. `zᴴ/z* < 2⌈A⌉/⌈A⌉ ≤ 2` → **ε = 1** □

**Worst case** (ratio → 2): 4n oggetti con pesi alternati ½, 1/2n, ½, 1/2n, ...
- Next Fit: 2n bin (ogni bin ha un oggetto di peso ½ e uno di 1/2n)
- Ottimo: n bin con coppia (½,½) + 1 bin con tutti gli oggetti 1/2n
- Ratio = 2n/(n+1) → 2 per n → ∞

## Knapsack — Euristiche Greedy

**Problema**: n oggetti con profitto pⱼ, peso wⱼ, capacità b. Massimizzare il profitto.
```
max  Σⱼ pⱼxⱼ
     Σⱼ wⱼxⱼ ≤ b
     xⱼ ∈ {0,1},  pⱼ, wⱼ, b interi
```

**Criteri di ordinamento**:
- **Euristica A**: pⱼ ≥ pⱼ₊₁ (prima gli oggetti con profitto maggiore)
- **Euristica B**: wⱼ ≤ wⱼ₊₁ (prima gli oggetti con peso minore)
- **Euristica C**: pⱼ/wⱼ ≥ pⱼ₊₁/wⱼ₊₁ (prima gli oggetti con miglior rapporto profitto/peso)

**Dantzig's formula** (LP relaxation via Euristica C):
1. Ordina per euristica C;
2. Inserisci oggetti in ordine finché si raggiunge l'**oggetto critico** s (primo che non entra);
3. L'oggetto s entra frazionalmente:

```
xⱼᴸᴾ = 1         j = 1,...,s-1
xⱼᴸᴾ = 0         j = s+1,...,n
xₛᴸᴾ = (b − Σⱼ₌₁ˢ⁻¹ wⱼ) / wₛ
```
Tutti i variabili hanno valore intero tranne l'oggetto critico s.

**Worst case di A**: n oggetti, p₁ = p (peso w₁ = b); altri n-1 oggetti con pⱼ = p-1, wⱼ = 1.
- Euristica A seleziona solo oggetto 1: zᴬ = p
- Ottimo: tutti gli altri n-1 oggetti: z* = (p-1)b
- Per b → ∞: performance infinitamente cattiva.

**Worst case di C**: n oggetti, b = kn; un oggetto con wmax = b, pmax = b-1 (ratio < 1); n-1 oggetti con wⱼ = 1, pⱼ = 1 (ratio = 1).
- C seleziona i piccoli (ratio 1 > ratio del grande): zᶜ = n-1
- z* = pmax = b-1 = kn-1; ratio = (kn-1)/(n-1) = k + (k-1)/(n-1) → ∞ per k → ∞

**Euristica C migliorata** — bound garantito ½:
```
z^greedy = max{zᶜ, pmax}
```
Idea: se il profitto ottenuto da C è minore del miglior singolo oggetto, prendo solo quell'oggetto.

**Dimostrazione** (z^greedy ≥ ½z*):  
Sia P̄ₛ = Σⱼ₌₁ˢ⁻¹ pⱼ (somma profitti items selezionati da C, senza item critico s).

Proprietà: `z^greedy ≥ zᶜ ≥ P̄ₛ` e `z^LP = P̄ₛ + (residuo frazionario) < P̄ₛ + pₛ`

**Caso A** (pₛ ≤ P̄ₛ):
```
z* < P̄ₛ + pₛ ≤ 2P̄ₛ ≤ 2zᶜ ≤ 2z^greedy → z^greedy ≥ ½z*
```

**Caso B** (pₛ > P̄ₛ):
```
z* < P̄ₛ + pₛ < 2pₛ ≤ 2pmax ≤ 2z^greedy → z^greedy ≥ ½z*
```

In entrambi i casi: `z^greedy ≥ ½z* = (1−ε)z*` con **ε = ½** □

## Riepilogo bounds garantiti

| Problema | Algoritmo | ε | Tipo bound |
|----------|-----------|---|------------|
| TSP (triangle inequality) | Tree Algorithm | 1 | `zᴴ ≤ 2z*` |
| Max Independent Set | Greedy | ∞ | nessuno |
| Min Vertex Cover | LP Rounding | 1 | `zᴴ ≤ 2z*` |
| Bin Packing | Next Fit | 1 | `zᴴ < 2z*` |
| Knapsack | Greedy C migliorato | ½ | `zᴴ ≥ ½z*` |

## Articoli correlati

- [[exact-algorithms]]
- [[ottimizzazione-combinatoria]]
- [[branch-and-cut-tsp]]
- [[metaeuristiche-introduzione]]

## Fonti

- `raw/Approximation Algorithms_2020.pdf` — slide del corso Optimization Algorithms, Prof. Renata Mansini, UniBS A.A. 2020/2021.
