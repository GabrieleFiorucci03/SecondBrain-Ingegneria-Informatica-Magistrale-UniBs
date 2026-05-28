---
tags: [calcolabilità, macchina-di-turing, decidibilità, indecidibilità, problema-della-fermata]
data_creazione: 2026-05-28
data_aggiornamento: 2026-05-28
fonti:
  - raw/1-Calcolabilita_con_integrazioni.pdf
---

# Teoria della Calcolabilità

Studia **cosa può essere calcolato** in linea di principio, indipendentemente dall'efficienza. Il modello di riferimento è la **Macchina di Turing (MT)**, che fornisce una definizione formale del concetto di calcolo. Tutti i risultati validi per le MT sono applicabili a qualunque altro sistema di calcolo o linguaggio di programmazione.

## Punti chiave

- **MTD** (Macchina di Turing Deterministica): nastro infinito + testina + stati + funzione di transizione δ
- **T-decidibile**: linguaggio L deciso da una MTD che termina sempre (accettazione o rifiuto)
- **T-semidecidibile**: linguaggio L accettato da una MTD (può non terminare su input ∉ L)
- Ogni linguaggio T-decidibile è T-semidecidibile, ma non viceversa
- **Tesi di Church-Turing**: ogni algoritmo è realizzabile da una MTD (postulato indimostrabile)
- **MTM** (Multinastro): più nastri, stessa potenza computazionale della MTD, ma maggiore efficienza
- **Problema della fermata**: indecidibile — non esiste MTD che decida se una MTD generica termina su un input generico
- **Teorema di Rice**: ogni proprietà non banale dei linguaggi T-semidecidibili è indecidibile

## Macchina di Turing Deterministica (MTD)

**Struttura**: nastro illimitato bidirezionale diviso in celle + testina di lettura/scrittura + unità di controllo con stati.

**Definizione formale**: M = ⟨Γ, b, Q, q₀, F, δ⟩ dove:
- Γ = alfabeto dei simboli di nastro
- b ∉ Γ = carattere blank (celle vuote)
- Q = insieme finito non vuoto di stati
- q₀ ∈ Q = stato iniziale
- F ⊆ Q = stati finali
- δ: (Q–F) × (Γ∪{b}) → Q × (Γ∪{b}) × {d,s,i} = funzione (parziale) di transizione

I movimenti della testina: **d** (destra), **s** (sinistra), **i** (immobile).

## Configurazione istantanea

Descrive lo stato completo della computazione: contenuto del nastro (porzione non-blank) + posizione testina + stato corrente. Si rappresenta come stringa: `wqy` (w = sinistra della testina, q = stato, y = simbolo sotto testina + destra).

- **Configurazione iniziale** c₀ = q₀x (x = input, testina sul primo simbolo)
- **Configurazione finale** (accettazione): wqy con q ∈ F
- **Configurazione di rifiuto**: nessuna transizione applicabile e q ∉ F
- **Determinismo**: da ogni configurazione esiste **al più una** configurazione successiva

## Linguaggi decisi e accettati

Una MTD M **accetta** x se la computazione da q₀x raggiunge una configurazione finale.
Una MTD M **rifiuta** x se termina in configurazione di rifiuto.

**T-decidibile**: L è deciso da M se M termina su ogni x ∈ Σ* e accetta ↔ x ∈ L.

**T-semidecidibile**: L è accettato da M se M accetta esattamente gli x ∈ L (su x ∉ L può non terminare).

**Relazione**: ogni L T-decidibile è T-semidecidibile; il viceversa è falso.

**MTD come trasduttori**: M **calcola** f: Σ* → Σ* se, per ogni x con f(x)=y, M raggiunge la config. finale xbqy con q∈F. La funzione f è detta **T-calcolabile**.

## MTD a nastro singolo: esempio

Una MTD che incrementa di 1 un numero in notazione binaria parte da destra, inverte i bit finché trova 0, poi si ferma. Funzione di transizione rappresentabile come matrice (stati×simboli) o grafo di transizione.

## Macchina di Turing Multinastro (MTM)

Mᵏ = ⟨Γ, b, Q, q₀, Z₀, F, δᵏ⟩ con k nastri, ciascuno con testina indipendente. Il nastro 1 = input; nastri 2..k inizialmente contengono Z₀.

**Potenza computazionale**: MTM ≡ MTD (stesso insieme di linguaggi decidibili/accettati). La MTM introduce solo efficienza, non nuova potenza. Ogni MTM a k nastri è simulabile da MTD con rallentamento polinomiale.

**Utilizzo convenzionale**: nastro 1 = input (sola lettura, testina in un solo verso); nastri 2..k-1 = lavoro; nastro k = output (sola scrittura, testina in un solo verso; solo per trasduttori).

## Tesi di Church-Turing

Tutti i modelli alternativi alla MTD (macchine a registri, λ-calcolo, funzioni ricorsive parziali, RAM) producono le stesse classi di linguaggi decidibili e funzioni calcolabili.

**Tesi** (postulato indimostrabile): ogni algoritmo espresso in qualunque modello di calcolo è realizzabile mediante una MTD.

Implicazione pratica: per dimostrare che un problema è decidibile/indecidibile, si può usare qualunque pseudocodice o linguaggio informale senza bisogno di costruire esplicitamente la MT.

## Problemi indecidibili

**Problema della fermata** (Halting Problem): dato M (MTD) e x (input), decidere se M termina su x.

H = {⟨M, x⟩ | M è una MTD che termina su input x}

**Teorema**: H è T-semidecidibile ma **non** T-decidibile.

**Dimostrazione** (diagonalizzazione di Cantor): supponiamo per assurdo che esista una MTD D che decide H. Costruiamo D' che su input M:
- se D(M,M) = "accetta" (M termina su se stesso) → D' entra in loop
- se D(M,M) = "rifiuta" (M non termina su se stesso) → D' accetta

Allora D'(D') porta a contraddizione: D' termina su D' ↔ D' non termina su D'. Assurdo.

**Conseguenza**: esistono problemi non risolvibili algoritmicamente (le MT/i programmi non risolvono tutto).

## Teorema di Rice

Ogni proprietà **non banale** dei linguaggi T-semidecidibili è indecidibile.

Una proprietà P è: **banale** se vale per tutti i linguaggi T-semidecidibili o per nessuno; **non banale** altrimenti.

Corollari indecidibili (tutti riducibili al problema della fermata):
- Decidere se L(M) = ∅
- Decidere se L(M) è finito/infinito
- Decidere se due MTD riconoscono lo stesso linguaggio
- Decidere se M accetta una specifica stringa

## Gerarchia delle classi di linguaggi

```
Linguaggi decidibili (T-decidibili)
    ⊊
Linguaggi semidecidibili (T-semidecidibili, RE)
    ⊊
Tutti i linguaggi su Σ*
```

La gerarchia è strettamente crescente: esistono linguaggi non T-semidecidibili (es. complemento del problema della fermata).

## Articoli correlati

- [[complessita-computazionale]]
- [[introduzione-algoritmi]]

## Fonti

- `raw/1-Calcolabilita_con_integrazioni.pdf` — Prof.ssa Marina Zanella & Prof. Alessandro Saetti, UniBS
