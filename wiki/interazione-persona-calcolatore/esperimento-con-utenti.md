---
tags: [hci, valutazione, esperimento, statistica, usability-testing]
data_creazione: 2026-05-29
data_aggiornamento: 2026-05-29
fonti:
  - raw/20-Esperimento - parte 1_COMPILED.pdf
  - raw/21-Esperimento - parte 2_COMPILED.pdf
---

# Esperimento con gli utenti

La valutazione empirica con utenti reali comprende due categorie principali: il **test di usabilità** (qualitativo, orientato agli sviluppatori) e l'**esperimento controllato** (quantitativo, orientato alla comunità scientifica).

## Punti chiave

- **Test di usabilità**: 5-10 utenti; con 5 si trovano l'85% dei problemi (curva di Nielsen); focus su tempo di completamento, errori, soddisfazione.
- **Esperimento controllato**: richiede confronto tra (almeno) due condizioni; usa statistica inferenziale per validare i risultati.
- La differenza chiave: il test di usabilità risponde a "il sistema ha problemi?"; l'esperimento risponde a "il design A è migliore del design B?".
- **Within-subjects**: lo stesso utente usa entrambe le condizioni (almeno 10 utenti).
- **Between-subjects**: utenti diversi per condizioni diverse (almeno 20 utenti per condizione).
- Il p-value confrontato con alpha (tipicamente 5%) determina la significatività statistica.

## Test di usabilità

Caratteristiche:
- Pochi utenti (5-10).
- Dati raccolti: tempo di completamento, numero/tipo di errori, video/audio, protocolli verbali.
- Questionari di soddisfazione e interviste post-task.
- Risultati utili agli sviluppatori per correggere i problemi.

**Regola dei 5 utenti di Nielsen**: con 5 utenti si trova l'85% dei problemi; aggiungere utenti porta rendimenti decrescenti; con 15 si trovano quasi tutti.

## Esperimento controllato

Struttura di un esperimento HCI:
1. **Ipotesi di ricerca**: es. "introdurre un nuovo stile di interazione migliora l'efficienza".
2. **Operazionalizzare le variabili**: definire cosa misurare (variabili dipendenti) e cosa variare (variabili indipendenti).
3. **Ipotesi nulla (H₀) e alternativa (H₁)**: H₀ = non ci sono differenze; H₁ = ci sono differenze.
4. **Livello di significatività (alpha)**: tipicamente 5% (o 1%). Alpha = probabilità di commettere un errore di tipo I (falso positivo).
5. **Predizione one-tailed o two-tailed**: two-tailed = si prevede differenza in qualsiasi direzione; one-tailed = si prevede differenza in una direzione specifica.
6. **Normalità della distribuzione**: applicare test di normalità per scegliere il test statistico corretto.
7. **Test statistico**: dipende dal tipo di variabili e dalla distribuzione.
8. **Eseguire il test e interpretare l'output**: confrontare p-value con alpha.
9. **Rigettare o non rigettare H₀**: se p-value < alpha → rigetto H₀ e concludo che le differenze sono reali.

## Misure e test statistici

| Tipo di variabile | Statistica descrittiva | Test inferenziale |
|---|---|---|
| **Scalare** (es. tempo) | Media, varianza, SD | t-test di Student, ANOVA/F-test |
| **Ordinale** (es. scala Likert) | Moda, mediana | Mann-Whitney (between), Wilcoxon (within) |
| **Nominale** (es. preferenza A vs B) | Frequenza | Pearson Chi-Square |

- **Test parametrici** (t-test, ANOVA): assumono distribuzione normale; più potenti.
- **Test non parametrici** (Mann-Whitney, Wilcoxon, Chi-Square): non assumono normalità; meno potenti.

**Significatività statistica ≠ rilevanza pratica**: una differenza può essere statisticamente significativa ma praticamente irrilevante (es. 0.1 secondi in più).

## Articoli correlati

- [[metodi-raccolta-dati]]
- [[usabilita]]
- [[valutazione-euristica]]
- [[walkthrough-cognitivo]]

## Fonti

- raw/20-Esperimento - parte 1_COMPILED.pdf
- raw/21-Esperimento - parte 2_COMPILED.pdf
