---
tags: [hci, fenomeni, comunicazione, cognizione-distribuita, co-evoluzione]
data_creazione: 2026-05-29
data_aggiornamento: 2026-05-29
fonti:
  - raw/8-Fenomeni-della-HCI_COMPILED.pdf
---

# Fenomeni dell'HCI

L'HCI studia una serie di fenomeni che emergono dall'interazione tra persone e sistemi informatici. Questi fenomeni aiutano a spiegare perché certi design funzionano e altri no, e quali proprietà sistemiche emergono dall'uso.

## Punti chiave

- **Communication gap**: distanza semantica tra il vocabolario/modello dell'utente e quello del sistema.
- **Co-evoluzione**: utenti e sistemi si modificano reciprocamente nel tempo; il design deve considerare questa dinamica.
- **Cognizione distribuita**: il pensiero non è solo nella testa — si distribuisce su artefatti, ambiente e altri agenti.
- **Grain problem**: il livello di granularità dell'analisi influenza ciò che si osserva e ciò che si progetta.
- I fenomeni emergenti rendono impossibile prevedere completamente il comportamento di un sistema prima del deploy.

## Communication Gap

Il gap di comunicazione tra utente e sistema nasce da vocabolari diversi: il sistema usa termini tecnici interni; l'utente usa il linguaggio del suo dominio applicativo. Il gap si misura con le distanze semantica e articolatoria del modello di Norman.

Un vocabolario condiviso riduce il gap. Il design deve adottare il vocabolario degli utenti (principio 2 di Nielsen: match tra sistema e mondo reale).

## Co-evoluzione

Utenti e sistemi si trasformano reciprocamente:
- Gli utenti imparano il sistema e cambiano il loro modo di lavorare.
- I sistemi vengono aggiornati in risposta ai comportamenti degli utenti.
- Le organizzazioni cambiano le loro procedure per adattarsi ai nuovi sistemi.

Conseguenza di design: non si può progettare un sistema "definitivo"; il design deve essere pensato per l'evoluzione e la manutenzione.

## Cognizione distribuita

Proposta da Hutchins: la cognizione umana non avviene solo nella mente del singolo individuo ma si distribuisce su:
- **Artefatti cognitivi**: strumenti, documenti, interfacce che "pensano" al posto nostro (es. lista della spesa, GPS).
- **Ambiente fisico**: la disposizione degli oggetti nello spazio aiuta il pensiero.
- **Altri agenti**: team, colleghi, AI.

Implicazione di design: l'interfaccia è un artefatto cognitivo; va progettata per supportare la cognizione distribuita, non per sostituirla.

## Grain Problem

Il livello di analisi scelto determina ciò che si osserva:
- Analisi a grana fine (singola azione, millisecondo): vedi i dettagli dell'interazione motoria.
- Analisi a grana grossa (task, sessione, giorno): vedi i pattern di uso e le strategie.

Scegliere la giusta granularità è cruciale per rilevare i problemi rilevanti.

## Articoli correlati

- [[modelli-di-interazione]]
- [[utente]]
- [[introduzione-hci]]
- [[hci-e-ai]]

## Fonti

- raw/8-Fenomeni-della-HCI_COMPILED.pdf
