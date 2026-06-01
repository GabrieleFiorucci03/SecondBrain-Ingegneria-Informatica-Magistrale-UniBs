---
tags: [hci, norman, modello, golfo, affordance]
data_creazione: 2026-05-29
data_aggiornamento: 2026-05-29
fonti:
  - raw/2-Modelli-di-interazione_COMPILED.pdf
---

# Modelli di interazione

I modelli di interazione forniscono framework concettuali per capire come avviene la comunicazione tra utente e sistema. Il più influente è il modello di Norman basato sul ciclo valutazione-esecuzione.

## Punti chiave

- **Ciclo di Norman**: l'utente formula un goal → pianifica un'azione → esegue → percepisce il feedback → valuta se il goal è stato raggiunto.
- **Golfo dell'esecuzione**: distanza tra ciò che l'utente vuole fare e ciò che il sistema permette di fare; composto da distanza semantica (gap tra intenzione e rappresentazione) e distanza articolatoria (gap tra rappresentazione e azione fisica).
- **Golfo della valutazione**: distanza tra l'output del sistema e la percezione/interpretazione dell'utente; anch'esso composto da distanza semantica e articolatoria.
- **Affordance**: proprietà percepita di un oggetto che suggerisce come usarlo (es. un bottone invita a premere). Le affordance buone riducono il golfo dell'esecuzione.
- **Modello basato sulla comunicazione**: il sistema e l'utente sono "agenti" che comunicano tramite un linguaggio condiviso; la qualità della comunicazione dipende dal vocabolario condiviso.
- Un buon design minimizza entrambi i golfi.

## Il ciclo valutazione-esecuzione di Norman

```
Goal
  └→ Intenzione
       └→ Sequenza di azioni (pianificazione)
            └→ Esecuzione dell'azione
                 └→ Percezione dello stato del sistema
                      └→ Interpretazione
                           └→ Valutazione (rispetto al goal)
```

**Golfo dell'esecuzione** (lato sinistro, utente → sistema):
- *Distanza semantica*: quanto il vocabolario del sistema corrisponde al vocabolario dell'utente.
- *Distanza articolatoria*: quanto è difficile tradurre l'intenzione in azioni fisiche sul sistema.

**Golfo della valutazione** (lato destro, sistema → utente):
- *Distanza semantica*: quanto l'output del sistema è comprensibile.
- *Distanza articolatoria*: quanto è visibile/percepibile l'output.

## Affordance

L'affordance (Gibson, poi Norman) è la relazione percepita tra un oggetto e le azioni possibili su di esso. Nel design di interfacce: un'icona che sembra un bottone tridimensionale ha l'affordance del "cliccare". Le affordance mal progettate costringono l'utente a consultare la documentazione.

## Modello basato sulla comunicazione

Modello alternativo: utente e sistema si scambiano messaggi tramite un linguaggio. Il problema della comunicazione è garantire che i messaggi abbiano lo stesso significato per entrambi i "parlanti". Si parla di *vocabolario condiviso* e *significato condiviso*. Questo modello è utile per analizzare sistemi con linguaggio naturale o sistemi conversazionali.

## Articoli correlati

- [[introduzione-hci]]
- [[fenomeni-hci]]
- [[stili-di-interazione]]
- [[usabilita]]

## Fonti

- raw/2-Modelli-di-interazione_COMPILED.pdf
