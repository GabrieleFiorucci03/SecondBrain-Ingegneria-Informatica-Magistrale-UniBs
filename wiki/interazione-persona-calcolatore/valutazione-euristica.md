---
tags: [hci, valutazione, euristica, nielsen, ispezione]
data_creazione: 2026-05-29
data_aggiornamento: 2026-05-29
fonti:
  - raw/12-Valutazione euristica_COMPILED.pdf
---

# Valutazione euristica

La valutazione euristica (Nielsen & Molich, 1990) è un metodo di ispezione dell'usabilità in cui un gruppo di valutatori esamina un'interfaccia e ne giudica la conformità ai [[principi-di-nielsen]]. È uno dei metodi di valutazione senza utenti più usati.

## Punti chiave

- I valutatori esaminano l'interfaccia individualmente e poi aggregano i risultati.
- Ogni problema trovato viene associato alla/e euristica/che violata/e e riceve un **severity rating** (0-4).
- Con **3-5 valutatori** si trovano il 65-75% dei problemi di usabilità (curva di Nielsen).
- Aggiungere valutatori porta rendimenti decrescenti: con 15 si trovano quasi tutti i problemi.
- Vantaggio: economico, veloce, non richiede utenti reali. Svantaggio: i valutatori possono trovare problemi "falsi positivi" o perdere problemi reali.

## Procedura

1. **Selezione dei valutatori**: esperti di usabilità (idealmente 3-5). Non gli sviluppatori del sistema.
2. **Briefing**: illustrare agli esperti il sistema, il dominio applicativo e gli utenti target.
3. **Ispezione individuale**: ogni valutatore esamina l'interfaccia autonomamente (2 sessioni: prima per farsi un'idea generale, poi più sistematica). Annota i problemi con riferimento all'euristica violata.
4. **Aggregazione**: si raccolgono tutti i problemi trovati; si elimina duplicati; si assegna il severity rating.
5. **Debriefing**: discussione dei risultati con il team di sviluppo.

## Severity Rating

| Valore | Significato |
|---|---|
| 0 | Non è un problema di usabilità |
| 1 | Problema cosmetico: bassa priorità |
| 2 | Problema minore: bassa priorità di fixing |
| 3 | Problema maggiore: alta priorità |
| 4 | Catastrofe: va corretto prima del rilascio |

Il severity rating è determinato da: frequenza (comune o rara?), impatto (quanto è difficile superarlo?), persistenza (succede una volta o più volte?).

## Quanti valutatori?

La curva di Nielsen mostra che:
- 1 valutatore trova ~35% dei problemi.
- 5 valutatori trovano ~75%.
- 15 valutatori trovano quasi il 100%.

Il punto ottimale costo/beneficio è 3-5 valutatori.

## Vantaggi e svantaggi

**Vantaggi**:
- Economico (nessun utente da reclutare).
- Veloce (può essere fatto in pochi giorni).
- Utile in fasi preliminari quando non si ha ancora un prototipo funzionante.

**Svantaggi**:
- Dipende dall'esperienza dei valutatori.
- Tende a trovare molti problemi cosmetici e pochi problemi gravi legati al contesto d'uso reale.
- Può produrre falsi positivi (problemi che gli utenti reali non avrebbero).

## Articoli correlati

- [[principi-di-nielsen]]
- [[walkthrough-cognitivo]]
- [[usabilita]]
- [[esperimento-con-utenti]]

## Fonti

- raw/12-Valutazione euristica_COMPILED.pdf
