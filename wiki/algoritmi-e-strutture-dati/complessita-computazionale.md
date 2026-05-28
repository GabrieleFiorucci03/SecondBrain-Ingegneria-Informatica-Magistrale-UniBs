---
tags: [complessità, P, NP, NP-completo, riduzione, PSPACE, classi-complessità]
data_creazione: 2026-05-28
data_aggiornamento: 2026-05-28
fonti:
  - raw/2-Complessita.pdf
---

# Teoria della Complessità Computazionale

Studia la quantità di risorse di calcolo (tempo, memoria) necessarie per risolvere un problema. Il modello di riferimento è la **Macchina di Turing (MT)**, ma le classificazioni che ne derivano coincidono con quelle di altri modelli ragionevoli. L'obiettivo principale è determinare se un problema è **trattabile** (risolvibile in tempo polinomiale).

## Punti chiave

- **Upper bound** O(g(n)): esiste un algoritmo che risolve il problema in O(g(n))
- **Lower bound** Ω(g(n)): ogni algoritmo risolve il problema in Ω(g(n))
- **Classe P**: problemi decisi in tempo polinomiale; P = co-P
- **Classe NP**: problemi verificabili in tempo polinomiale (def. certificati) oppure accettati da algoritmi non deterministici in tempo polinomiale
- **NP-completo**: i problemi più difficili in NP — se uno è in P allora P=NP
- **Riducibilità**: L₁ ≤_P L₂ significa che risolvere L₂ risolve anche L₁
- Gerarchia confermata: P ⊆ NP ∩ co-NP ⊆ PSPACE; P ≠ PSPACE (dimostrato); P=NP ancora aperto

## Tipologie di problemi

| Tipo | Definizione | Esempio |
|---|---|---|
| **Decisionale** | π: I → {T,F} | Il grafo G ha una cricca di k vertici? |
| **Di ricerca** | f: I → O, trovare y ∈ O | Trovare la cricca di k vertici |
| **Di enumerazione** | ψ: I → ℕ, quante soluzioni? | Contare le cricche di k vertici |
| **Di ottimizzazione** | max/min μ(x,y) su y ammissibili | Trovare la cricca massima |

Ogni problema di ricerca ha un problema decisionale associato. Le classi P e NP classificano i problemi decisionali.

**Codifica**: una codifica è ragionevole se usa base ≥ 2 (no unaria artificialmente lunga). Due codifiche polinomialmente correlate danno la stessa classificazione di complessità.

## Classe P

**Algoritmo A decide L** in tempo polinomiale se: ∀x ∈ Σ*, A accetta x in tempo poly(|x|) se x ∈ L, rifiuta se x ∉ L.

**Classe P** = insieme dei linguaggi decisi in tempo polinomiale.

Equivalentemente: P = insieme dei linguaggi *accettati* in tempo polinomiale (perché si può simulare l'accettatore per c·nᵏ passi e rifiutare esplicitamente se non accetta).

Esempio in P: **GRAFO-BIPARTITO** — algoritmo deterministico in O(|V|+|E|) che colora il grafo con 2 colori.

## Classe NP

**Def. 1 (non-determinismo)**: NP = linguaggi accettati in tempo polinomiale da algoritmi non deterministici (in almeno una computazione su x ∈ L, output = 1 in poly passi).

**Def. 2 (certificati)**: NP = linguaggi *verificati* in tempo polinomiale — esiste un certificato y tale che A(x, y) = 1 in poly(|x|) passi.

Le due definizioni sono equivalenti.

**Esempio NP**: DECISIONE-CRICCA (G, k): certificato = insieme V' dei vertici; verifica: |V'| ≥ k e ∀u,v ∈ V', (u,v) ∈ E — eseguibile in O(k²).

**P ⊆ NP**: ogni L ∈ P è verificato in tempo polinomiale (il certificato è ignorato).

## co-P e co-NP

**co-P** = {Σ*–L | L ∈ P} = P (P è chiusa per complementazione).

**co-NP** = complementi dei linguaggi in NP.

P ⊆ NP ∩ co-NP. Non è noto se NP = co-NP.

**Esempio co-NP**: decidere se G NON è hamiltoniano è co-NP (il certificato di inammissibilità è la permutazione; la verifica richiederebbe controllare tutte le permutazioni → certificato non polinomiale → non si sa se è in NP).

## NP-completezza e riducibilità

**Riducibilità in tempo polinomiale** (L₁ ≤_P L₂): esiste f : Σ* → Σ* calcolabile in tempo polinomiale tale che x ∈ L₁ ⟺ f(x) ∈ L₂.

**Lemma**: se L₁ ≤_P L₂ e L₂ ∈ P allora L₁ ∈ P.

**NP-difficile** (NP-hard): L t.c. ∀L' ∈ NP, L' ≤_P L.
**NP-completo** (NPC): L ∈ NP e L è NP-hard.

**Teorema**: se qualsiasi L ∈ NPC è in P, allora P = NP.

**Procedura per dimostrare L ∈ NPC**:
1. Dimostrare L ∈ NP (dare il certificato e l'algoritmo di verifica)
2. Scegliere L' ∈ NP-hard noto
3. Descrivere f che trasforma ogni x ∈ L' in f(x) ∈ L
4. Dimostrare x ∈ L' ⟺ f(x) ∈ L
5. Dimostrare che f è calcolabile in tempo polinomiale

## Problemi NP-completi fondamentali

**CIRCUIT-SAT**: un circuito combinatorio booleano (AND, OR, NOT) è soddisfacibile? → NPC (primo problema dimostrato NPC per costruzione diretta dalla MT).

**SAT**: una formula booleana con n variabili e m operatori è soddisfacibile? → NPC. Dimostrazione: CIRCUIT-SAT ≤_P SAT (il circuito diventa una formula con una variabile per filo).

**3-CNF-SAT**: SAT ristretto a formule in Forma Normale Congiuntiva con esattamente 3 letterali per clausola → NPC. Riduzione: SAT ≤_P 3-CNF-SAT tramite albero di parsing + variabili ausiliarie + conversione DNF → CNF.

**CLIQUE**: un grafo G ha una cricca di k vertici? → NPC. Riduzione: 3-CNF-SAT ≤_P CLIQUE (ogni clausola C_r = l₁ᵣ ∨ l₂ᵣ ∨ l₃ᵣ genera una tripla di nodi; archi tra nodi di clausole diverse con letterali non complementari; φ soddisfacibile ⟺ G ha cricca di k=n° clausole nodi).

**VERTEX-COVER**: G ha una copertura di vertici di al più k vertici? → NPC.

**TSP** (Travelling Salesman): giro del commesso viaggiatore di costo ≤ k su grafo completo pesato → NPC.

**SUBSET-SUM**: ∃S' ⊆ S t.c. Σ_{s∈S'} s = t? → NPC.

**SCHEDULING**: scheduling di job con release time, deadline e durata → NPC.

## Cosa fare con i problemi NP-completi

1. **Input piccoli**: algoritmo esponenziale può essere accettabile
2. **Casi speciali**: certi sottoproblemi sono in P
3. **Algoritmi di approssimazione**: soluzione quasi ottima in tempo polinomiale, con rapporto di approssimazione ρ(n) = max(C/C*, C*/C)
   - Schemi di approssimazione: input (istanza, ε), (1+ε)-approssimato
   - Polinomiale: poly in n per ε fissato
   - Totalmente polinomiale (PTAS): poly in n e 1/ε

## Classe PSPACE

**PSPACE** = linguaggi decisi in spazio polinomiale (memoria poly(n)).

**Relazioni**: P ⊆ NP ⊆ PSPACE; PSPACE = co-PSPACE; co-NP ⊆ PSPACE.

Gerarchia confermata: P ⊊ PSPACE (dimostrato; P=NP ancora aperto).

**PSPACE-completo**: esempio **QSAT** (Quantified 3-CNF-SAT) — formula con quantificatori ∃∀ alternati. Algoritmo QSAT ∈ PSPACE usa spazio S(n) ≤ n·poly(n).

Molti 2-player games sono PSPACE-completi.

```
P ⊆ NP ∩ co-NP ⊆ NPC ⊆ PSPACE
```

## Articoli correlati

- [[introduzione-algoritmi]]
- [[calcolabilita]]
- [[grafi]]

## Fonti

- `raw/2-Complessita.pdf` — Prof. Alessandro Saetti & Prof.ssa Marina Zanella, UniBS
