---
tags: [ottimizzazione, metaeuristica, vns, local-search, vicinato-variabile, tsp]
data_creazione: 2026-06-02
data_aggiornamento: 2026-06-02
fonti:
  - raw/VNS_2021.pdf
---

# Variable Neighborhood Search (VNS)

Metaeuristica che sistematicamente cambia il **vicinato** durante la ricerca, esplorando vicinati progressivamente più distanti dalla soluzione corrente. Ogni volta che la local search fallisce in un vicinato, si allarga il raggio; ogni miglioramento riporta la ricerca al vicinato più piccolo. Semplice, robusta, ad ampia applicabilità.

## Punti chiave

- Usa un insieme finito di strutture di vicinato Nₖ (k=1,...,k_max) ordinate per dimensione crescente.
- Il cambio di vicinato è **sistematico**: si parte da N₁ (più piccolo), si allarga se la local search non trova miglioramento.
- Ogni mossa si accetta **solo se migliora** la soluzione incumbente (diversamente da [[tabu-search]]).
- La randomizzazione nel passo di shaking evita il ciclismo.
- **VND** (Variable Neighborhood Descent): variante deterministica che analizza completamente ogni vicinato.
- Tre varianti per grandi istanze: **RVNS** (senza local search), **VNDS** (decomposizione), **SVNS** (valuation function).

## Features principali

- Definizione di un insieme finito di strutture di vicinato Nₖ (k=1,...,k_max).
- Cambio sistematico del vicinato durante la ricerca.
- Nessuna traiettoria definita: esplorazione di vicinati progressivamente più distanti.
- Si sposta a una nuova soluzione **solo se migliora** la incumbente.
- Uso di un metodo di local search per trovare ottimi locali nei vicinati generati.

## Algoritmo VNS

**Inizializzazione**:
1. Definisci un insieme finito di strutture Nₖ (k=1,...,k_max).
2. Trova una soluzione iniziale ammissibile x.
3. Decidi la stopping rule.

**Main Loop** (ripeti finché stopping rule non è soddisfatta):
```
1. k ← 1
2. Until k = k_max ripeti:
   (a) Shaking:    x' ← random(Nₖ(x))   [soluzione casuale nel k-esimo vicinato]
   (b) Local search: x'' ← LocalSearch(x')  [ottimo locale a partire da x']
   (c) Move or not:
       if f(x'') < f(x):
           x ← x''
           k ← 1                          [restart dal vicinato più piccolo]
       else:
           k ← k + 1                      [allarga il vicinato]
```

**Osservazioni**:
1. VNS è un **metodo di discesa random** che usa *first improvement*.
2. La soluzione x' al passo 2a è generata casualmente per evitare il ciclismo.
3. I vicinati successivi Nₖ possono essere **annidati** (nested).
4. Stopping rules tipiche: max CPU time, max iterazioni, max iterazioni dall'ultimo miglioramento.

## Variable Neighborhood Descent (VND)

Variante che **analizza completamente** ogni vicinato senza randomizzazione.

```
Inizializzazione:
  - Definisci Nₖ (k=1,...,k_max)
  - Trova soluzione iniziale x

Main Loop (ripeti finché nessun miglioramento):
  1. k ← 1
  2. Until k = k_max ripeti:
     (a) Neighborhood exploration: x' ← best in Nₖ(x)
     (b) Move or not:
         if f(x') < f(x): x ← x', k ← 1
         else:             k ← k + 1
```

## Enhancements

- **Descent-ascent**: al passo 2c, move verso x'' con una certa probabilità anche se x'' è peggiore della soluzione corrente.
- **Best improvement**: al passo 2c, move verso il vicinato k* che è il migliore tra tutti i vicinati possibili.
- Al passo 2a, seleziona x' come il migliore tra l soluzioni casuali generate nel k-esimo vicinato.
- Parametri **k_min** e **k_step**: il restart usa k←k_min (invece di k←1) e il passo usa k←k+k_step (invece di k←k+1).
  - Valori alti di k_min e/o k_step → **diversificazione**
  - Valori bassi → **intensificazione**

## Come implementare VNS

Prima di usare VNS su un problema combinatorio, rispondere a:
1. Quale sequenza di vicinati Nₖ usare?
2. Quale valore assegnare a k_max?
3. In quale ordine esplorare i vicinati?
4. Quale strategia usare per cambiare vicinato?
5. Quale metodo di local search usare?
6. Quale stopping rule implementare?

## Applicazione al TSP

**Metrica**: distanza ρ(x₁, x₂) = cardinalità della differenza simmetrica (numero di archi in cui le soluzioni differiscono). ρ è una metrica su S.

**Vicinati**: k-OPT neighborhoods:
```
Nₖ(x) = { x' | ρ(x, x') = k, x ∈ S },  k = 2,...,n
```

**Due versioni implementate**:
- **VNS-1**: vicinati = k-OPT con k_max = n; local search al passo 2b = 2-OPT (O(n²)).
- **VNS-2**: stessi vicinati; local search = 2-OPT sul grafo ridotto eliminando r% dei nodi più lontani da ogni cliente.

**Risultati** (100 trials, problemi Euclidi random):
- VNS-2 produce un miglioramento medio del **4.43%** rispetto al 2-OPT standard.
- VNS-2 migliora di **0.75%** rispetto a GENIUS (algoritmo a inserimento) a parità di CPU time.

## Varianti per problemi di grandi dimensioni

### RVNS — Reduced Variable Neighborhood Search

**Obiettivo**: aumentare l'efficienza a scapito della qualità.

**Caratteristiche**:
1. Elimina la fase di local search.
2. Soluzioni generate casualmente in vicinati progressivamente più distanti.
3. Move solo se la soluzione è migliore.

Adatto per problemi dove serve una buona soluzione rapidamente.

### VNDS — Variable Neighborhood Decomposition Search

**Obiettivo**: migliorare sia efficienza che efficacia della ricerca.

**Caratteristiche**:
1. VNS combinato con un metodo di decomposizione.
2. Durante la local search, tutti gli attributi (variabili) di una soluzione sono fissati tranne k.
3. Il problema k-dimensionale viene risolto nello spazio delle variabili non fissate (scelte casualmente).
4. Il VNS base può essere usato come local search → **recursive VNS** a due livelli.

**Algoritmo** (Main Loop, al passo 2b):
```
(a) Shaking: x' ← random in Nₖ(x); y = x'\x (attributi di x' non in x)
(b) Local search: ottimo locale y' in spazio y; x'' = (x'\y) ∪ y' = (x'\y) ∪ y'
(c) Move or not: if f(x'') < f(x): x←x'', k←1; else k←k+1
```

### SVNS — Skewed Variable Neighborhood Search

**Obiettivo**: analizzare la topologia degli ottimi locali per trovare soluzioni di qualità lontane dalla incumbente.

**Caratteristica principale**: una **valuation function** sostituisce la funzione obiettivo nel criterio di accettazione.

**Inizializzazione**: definisci Nₖ, trova x iniziale, imposta x_opt←x, f_opt←f(x), scegli α.

**Algoritmo** (passo 2c/2d):
```
(c) Improve or not: if f(x'') < f_opt: f_opt←f(x''), x_opt←x''
(d) Move or not: if f(x'') − α·ρ(x, x'') < f(x): x←x'', k←1
                 else: k←k+1
```

Dove ρ(x, x'') è la distanza tra x'' e x incumbente. Il termine α·ρ(x, x'') "premia" le soluzioni lontane anche se leggermente peggiori.

## Conclusioni

VNS è un metodo:
1. **Semplice** e di ampia applicabilità;
2. **Efficiente ed efficace**: soluzioni di alta qualità in tempi ragionevoli;
3. **Robusto**: funziona bene per problemi diversi;
4. **User-friendly**: facile da capire e implementare;
5. **Innovativo**: consente sviluppo di varianti e miglioramenti.

## Articoli correlati

- [[alns]]
- [[tabu-search]]
- [[grasp]]
- [[metaeuristiche-introduzione]]
- [[branch-and-cut-tsp]]

## Fonti

- `raw/VNS_2021.pdf` — slide del corso Optimization Algorithms, Prof. Renata Mansini, UniBS A.A. 2021/2022.
