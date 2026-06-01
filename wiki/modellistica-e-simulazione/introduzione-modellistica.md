---
tags: [modellistica, simulazione, sistemi-dinamici, LTI, state-space]
data_creazione: 2026-05-30
data_aggiornamento: 2026-05-30
fonti:
  - raw/MODSIM_L1_Intro.pdf
---

# Introduzione alla Modellistica e Simulazione

Corso UniBS tenuto dal Prof. C. Carnevale e Ing. L. Sangiorgi. L'obiettivo è fornire una metodologia di modellizzazione applicabile a sistemi eterogenei e introdurre strumenti software (MATLAB/Simulink) per la simulazione. Testo di riferimento: Benvenuti, De Santis, Farina — *Sistemi Dinamici* (McGraw-Hill).

## Punti chiave

- **Modello**: rappresentazione della realtà. Un modello matematico è un'*immagine matematica* in grado di descrivere in maniera soddisfacente l'evoluzione di un fenomeno.
- **Citazione fondamentale**: "Tutti i modelli sono sbagliati. Alcuni modelli sono utili." (G. Box)
- I **sistemi dinamici** hanno memoria: lo stesso input in momenti diversi produce output differenti perché il sistema è in uno **stato** diverso.
- I sistemi si classificano in **statici** (no memoria, es. resistore: v=Ri) e **dinamici** (con memoria, es. condensatore: i=C dv/dt).
- Due rappresentazioni principali: **forma Input-Output** e **forma State-Space** (spazio degli stati).
- I sistemi **LTI** (Lineari Tempo Invarianti) sono un caso speciale molto trattabile, usato come approssimazione locale dei sistemi reali.

## Sistemi dinamici: il concetto di stato

Un **sistema dinamico** è una rappresentazione di fenomeni naturali o artificiali caratterizzati da parti che interagiscono e si modificano nel tempo (schema: u → S → y).

Lo **stato** è l'informazione interna del sistema che, insieme all'ingresso, determina l'uscita. Perciò lo stesso ingresso applicato in due momenti diversi produce uscite diverse (il sistema si trova in stati diversi).

**Rappresentazione matematica** (sistemi tempo continuo):
- *Forma dinamica generale:* equazione differenziale che lega y alle sue derivate e all'ingresso u
- *Forma State-Space:*
  - Ẋ = f(t, X, U)
  - Y = g(t, X, U)

**Sistemi tempo discreto** (State-Space):
- X(t+1) = f(t, X(t), U(t))
- Y(t) = g(t, X(t), U(t))

## Sistemi LTI (Lineari Tempo Invarianti)

Per sistemi LTI le funzioni f e g sono lineari, quindi la forma State-Space diventa:

**Tempo continuo:**
- Ẋ = AX + BU
- Y = CX + DU

**Tempo discreto:**
- X(t+1) = AX(t) + BU(t)
- Y(t) = CX(t) + DU(t)

Proprietà dei sistemi LTI:
- Punti di equilibrio calcolati risolvendo AX + BU = 0
- Numero di punti di equilibrio: 0, 1, o infiniti
- Stabilità: **globale** (a differenza dei sistemi nonlineari che hanno stabilità locale)

I sistemi LTI sono centrali per il **progetto del controllore**; sistemi reali nonlineari vengono spesso linearizzati attorno a un punto di equilibrio per sfruttare questa semplicità.

## Sistemi nonlineari

**DEF:** Sistemi rappresentati da modelli in cui la mappa di transizione dello stato e/o la trasformazione di uscita sono nonlineari (Ẋ = f(t,X,U), Y = g(t,X,U) con f, g nonlineari).

Differenze rispetto ai sistemi lineari:

| | Sistemi lineari | Sistemi nonlineari |
|---|---|---|
| Modello | Ẋ = AX+BU | Ẋ = f(t,X,U) |
| Punti di equilibrio | 0, 1, infiniti | 0, 1, …, n, infiniti |
| Stabilità | Globale | Locale |

La **stabilità** nei sistemi nonlineari è una caratteristica locale dei punti di equilibrio, studiata tramite il sistema **linearizzato attorno al punto di equilibrio** (matrice Jacobiana A valutata in x̄).

## Articoli correlati

- [[modelli-fisici]]
- [[modelli-epidemiologici]]
- [[modelli-popolazioni]]
- [[sistemi-nonlineari-e-identificazione]]

## Fonti

- raw/MODSIM_L1_Intro.pdf
