---
tags: [modellistica, identificazione, ARX, minimi-quadrati, black-box, grey-box, validazione]
data_creazione: 2026-05-30
data_aggiornamento: 2026-05-30
fonti:
  - raw/MODSIM_L6_ID1.pdf
  - raw/MODSIM_L7_ID2.pdf
---

# Identificazione di Modelli

L'**identificazione** è il processo di costruzione di un modello matematico di un sistema a partire da **dati misurati di ingresso e uscita**, quando il modello non è noto o non è derivabile da principi fisici.

Si usa quando: il sistema è troppo complesso da modellare, è già ingegnerizzato da altri, o si ha necessità di un modello rapido nel breve periodo.

## Punti chiave

- **Obiettivo**: trovare θ_best che minimizza la distanza D(ŷθ, ȳ) tra uscita stimata e uscita misurata.
- **Black-box**: nessuna conoscenza del sistema → modello I-O (tipicamente ARX).
- **Grey-box**: conoscenza parziale della struttura → modello nello spazio degli stati con parametri ignoti.
- La distinzione black/grey-box non è definitiva: dipende dalla conoscenza disponibile.
- I **modelli ARX** sono la scelta standard per l'identificazione black-box a tempo discreto.
- Il **metodo dei minimi quadrati** in forma chiusa: θ = (M̄ᵀM̄)⁻¹M̄ᵀȳ.
- Differenza cruciale: **previsione** (usa uscite misurate precedenti) vs **simulazione** (usa uscite stimate precedenti).

## Formulazione del problema

Dati:
- Serie temporale misurata (ū, ȳ) di ingressi e uscite del sistema
- Famiglia di modelli M(θ, u), dove θ sono i parametri da stimare
- Metrica D(ŷθ, ȳ) che misura la distanza tra uscita stimata e misurata

**Obiettivo:** trovare θ_best = argmin_θ D(ŷθ, ȳ)

Quando D è la somma dei quadrati degli errori → **metodo dei minimi quadrati** (Least Squares):
```
D(ŷθ, ȳ) = Σₜ (ŷθ(t) − ȳ(t))²
```

## Famiglia di modelli

```
Dati I-O disponibili
    │
    ├─ Conosco qualcosa del sistema? ─NO→ Modelli Black-Box (relazioni I-O)
    │
    └─ YES → Modelli Grey-Box (spazio degli stati)
```

La distinzione non è definitiva:
- Un problema black-box può diventare grey-box coinvolgendo esperti del sistema
- Un problema grey-box può essere approcciato black-box se la struttura nota non porta a un problema risolvibile

## Modelli ARX

I modelli **A**uto**R**egressivi con ingressi e**X**ogeni (ARX) sono la famiglia standard per l'identificazione black-box.

**Forma scalare** (SISO):
```
y(t) = α₁y(t−1) + ... + αₙy(t−n) + β₀u(t) + ... + βₘu(t−m)
```

**Forma matriciale:**
```
y(t) = M(t) * θ
M(t) = [y(t−1),...,y(t−n), u(t),...,u(t−m)]
θ = [α₁,...,αₙ, β₀,...,βₘ]ᵀ
```

**Note:**
- n = ordine parte autoregressiva, m = ordine parte esogena
- y e u possono essere vettori (MIMO)
- Le misure sono disponibili solo a istanti di campionamento → il problema si risolve a **tempo discreto**

## Previsione vs Simulazione

Dato un modello ARX identificato, ŷθ(t) si calcola in due modi:

| Modalità | Formula | Descrizione |
|---|---|---|
| **Previsione** | ŷθ = M̄ * θ | Usa le uscite **misurate** negli istanti precedenti |
| **Simulazione** | ŷθ = M̂ * θ | Usa le uscite **stimate** negli istanti precedenti |

La simulazione richiede una condizione iniziale e propaga gli errori; la previsione usa i dati reali e non propaga errori ma non è autonoma.

## Metodo dei Minimi Quadrati (Previsione ARX)

Configurazione: sistema SISO, modello ARX, metrica euclidea, modalità previsione.

```
argmin_θ D(ŷθ, ȳ) = argmin_θ (M̄θ − ȳ)ᵀ(M̄θ − ȳ)
```

Derivando rispetto a θ e ponendo = 0:
```
M̄ᵀM̄θ = M̄ᵀȳ
⟹ θ = (M̄ᵀM̄)⁻¹M̄ᵀȳ    (soluzione in forma chiusa)
```

dove M̄ è la matrice con una riga per ogni istante di campionamento.

## Pipeline dal sistema al modello

Processo pratico completo per costruire un modello identificato:

```
Raccolta Dati → Dataset → Analisi dataset → Suddivisione dataset
                                    ↓                    ↓
                          Dataset Identificazione   Dataset Validazione
                                    ↓
                          Selezione del modello
                                    ↓
                          Stima dei parametri
                                    ↓
                          Valutazione Taratura ─NO→ (torna a Selezione)
                                    ↓ SI
                          Validazione ─NO→ (torna a Raccolta Dati)
                                    ↓ SI
                          Modello Validato
```

### Raccolta dati

Domande pratiche:
1. **Quali I/O identificare?** (es. essiccatore industriale: output = temperatura bulbo secco/umido/umidità materiale; input = flusso carburante/velocità aria/flusso materiale)
2. **Possiamo fare test?**
   - Chiedere i valori tipici di lavoro degli input
   - Capire quando l'uscita raggiunge un pseudo-regime e in quanto tempo
   - Effettuare prove "a gradini" attorno ai valori tipici
3. **Esistono dati già misurati?**
   - Verificare che abbiano contenuto informativo (non sempre lo stesso valore)

### Analisi e suddivisione del dataset

**Selezione degli input:**
- Per il controllo: variabili manipolabili + eventuali disturbi misurabili
- Per la previsione: variabili che massimizzano la capacità predittiva

**Selezione degli output:** variabili di interesse + possibili trasformazioni che linearizzano il modello.

**Gestione dati non validi** (mancanti o palesemente sbagliati):
- *Approccio storico*: eliminare le righe della matrice M̄ che coinvolgono dati non validi (bisogna togliere la n-pla completa)
- *Approccio moderno*: ricostruire i dati mancanti con tecniche di imputazione

**Suddivisione:** dataset → parte identificazione + parte validazione (separati, non sovrapposti).

## Articoli correlati

- [[introduzione-modellistica]]
- [[nonlinearita-e-linearizzazione]]

## Fonti

- raw/MODSIM_L6_ID1.pdf
- raw/MODSIM_L7_ID2.pdf
