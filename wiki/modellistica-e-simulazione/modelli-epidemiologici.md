---
tags: [modellistica, epidemiologia, SIR, SEIR, sistemi-nonlineari, dinamica-popolazioni]
data_creazione: 2026-05-30
data_aggiornamento: 2026-05-30
fonti:
  - raw/MODSIM_L3_Epi.pdf
---

# Modelli Epidemiologici

I **modelli epidemiologici** rappresentano l'evoluzione del contagio di una malattia in una popolazione. Sono sistemi a **tempo continuo** (tipicamente in letteratura), nonlineari, costruiti come **modelli di transizione tra stati** (compartimenti).

La **variabile di stato** è il numero di individui in ciascun compartimento; le equazioni descrivono i **flussi** tra compartimenti in funzione del tempo.

## Punti chiave

- Struttura a compartimenti: la popolazione è suddivisa in gruppi (S, I, R, E, D, V…).
- Il flusso S→I è proporzionale al **prodotto S(t)·I(t)** (probabilità di incontro × infettività b).
- Il flusso I→R è proporzionale a **I(t)** (tasso di guarigione g).
- Il modello **SIR base** è chiuso: la dinamica dell'infezione è molto più rapida della variazione demografica.
- Ogni variante (SIRD, SEIR, SEIS…) aggiunge compartimenti per modellare aspetti specifici (mortalità, incubazione, immunità temporanea, vaccinazione).
- Conservazione: in SIR chiuso, Ṡ + İ + Ṙ = 0 (la popolazione totale rimane costante).

## Modello SIR

Compartimenti: **S**(usceptible), **I**(nfectious), **R**(ecovered/immune).

```
Susceptible →[bS(t)I(t)]→ Infectious →[gI(t)]→ Recovered
```

**Equazioni:**
- Ṡ(t) = −b·S(t)·I(t)
- İ(t) = b·S(t)·I(t) − g·I(t)
- Ṙ(t) = g·I(t)

**Parametri:** b = infettività (tiene conto di probabilità di incontro e trasmissibilità); g = tasso di guarigione.

**Ipotesi:** sistema chiuso (nascite/morti trascurabili rispetto alla velocità del contagio).

## SIR con nascite e decessi

Quando la dinamica epidemica è lenta rispetto alla variazione demografica, si aggiungono:
- Flusso in entrata (nascite): Δ (tasso costante)
- Flusso in uscita (decessi naturali): μ·X(t) per ogni compartimento X

**Equazioni:**
- Ṡ(t) = Δ − b·S(t)·I(t) − μ·S(t)
- İ(t) = b·S(t)·I(t) − g·I(t) − μ·I(t)
- Ṙ(t) = g·I(t) − μ·R(t)

## Modello SIRD

Estende il SIR con decessi causati dalla malattia (compartimento **D**ecessed).

```
S →[bSI]→ I →[gI]→ R →[dI]→ D
```

**Equazioni:**
- Ṡ(t) = Δ − b·S(t)·I(t) − μ·S(t)
- İ(t) = b·S(t)·I(t) − g·I(t) − μ·I(t) − d·I(t)
- Ṙ(t) = g·I(t) − μ·R(t)
- Ḋ(t) = d·I(t)

dove d è il tasso di mortalità per la malattia.

## Modello SEIR

Aggiunge un compartimento **E**(xposed): individui contagiati ma ancora in periodo di **incubazione**, non ancora contagiosi.

```
S →[bSI]→ E →[aE]→ I →[gI]→ R
```

**Equazioni (con demografia):**
- Ṡ(t) = Δ − b·S(t)·I(t) − μ·S(t)
- Ė(t) = b·S(t)·I(t) − a·E(t) − μ·E(t)
- İ(t) = a·E(t) − g·I(t) − μ·I(t)
- Ṙ(t) = g·I(t) − μ·R(t)

dove a = tasso di fine incubazione.

**Variante con contagio da esposti:** se anche gli esposti possono contagiare (con infettività c < b):
- Ṡ(t) = Δ − b·S·I − c·S·E − μ·S
- Ė(t) = b·S·I + c·S·E − a·E − μ·E
- (resto invariato)

## Modello SEIS

Usato quando l'infezione **non garantisce immunità**: dopo la guarigione gli individui tornano suscettibili.

```
I →[gI]→ S (invece di R)
```

**Equazioni:**
- Ṡ(t) = Δ − b·S·I + g·I − μ·S
- Ė(t) = b·S·I − a·E − μ·E
- İ(t) = a·E − g·I − μ·I

## Modellizzazione della vaccinazione

Si aggiunge il compartimento **V**(accinati), alimentato da:
- P% dei neonati vaccinati alla nascita
- Frazione q dei suscettibili che si vaccinano

**Equazioni (con vaccinazione dei neonati e campagna vaccinale):**
- Ṡ(t) = Δ(1−P) − b·S·I − q·S − μ·S
- İ(t) = b·S·I − g·I − μ·I
- Ṙ(t) = g·I − μ·R
- V̇(t) = PΔ + q·S − μ·V

## Articoli correlati

- [[introduzione-modellistica]]
- [[modelli-popolazioni]]

## Fonti

- raw/MODSIM_L3_Epi.pdf
