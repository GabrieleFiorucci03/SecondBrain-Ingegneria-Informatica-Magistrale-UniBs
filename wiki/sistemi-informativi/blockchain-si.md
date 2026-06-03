---
tags: [sistemi-informativi, blockchain, distributed-ledger, smart-contract, consenso, supply-chain]
data_creazione: 2026-06-03
data_aggiornamento: 2026-06-03
fonti:
  - raw/L-BLOCKCHAIN-SI.pdf
---

# Blockchain nei Sistemi Informativi

La blockchain è una tecnologia di **ledger distribuito (DLT)** applicata ai processi aziendali cooperativi inter-organizzativi. Risponde al problema fondamentale della **mancanza di fiducia (trust)** tra entità che cooperano (supply chain, shipping management). La blockchain è meno efficiente di un database centralizzato ma molto più robusta rispetto a manipolazioni e alterazioni.

## Punti chiave

- Un **Distributed Ledger** è un archivio di transazioni append-only, distribuito e replicato su vari elaboratori.
- Una **Blockchain** è un distributed ledger strutturato in una catena di blocchi collegati tramite hash crittografici.
- Ogni blocco contiene: lista di transazioni + hash del blocco precedente + proprio hash.
- Il **consenso** è il meccanismo che permette ai partecipanti di accordarsi sull'aggiunta di nuovi blocchi; tre famiglie: Proof of Work, Proof of Stake, Proof of Authority.
- Tre tipi di blockchain: **Libere** (unpermissioned, es. Bitcoin), **Controllate/Consorziate** (permissioned, es. Hyperledger Fabric), **Private**.
- Applicazione SI: tracciabilità supply chain, certificazioni, pagamenti inter-organizzativi.

## Processi cooperativi e il problema del trust

La cooperazione tra organizzazioni genera valore (Business Networks) ma pone problemi di **fiducia (trust)** tra le entità che cooperano. I processi cooperativi coinvolgono:

- **Partecipanti**: clienti, fornitori, governi, enti regolatori — ognuno con identità e ruoli specifici
- **Transazioni**: trasferimento di un asset (es. "John vende un'auto ad Anthony")
- **Contratti**: condizioni perché la transazione avvenga (es. "se Anthony paga, l'auto passa a John")
- **Asset**: tutto ciò che può essere posseduto o controllato per produrre valore
  - Tangibili: proprietà immobiliare, merci fisiche
  - Intangibili: finanziari (obbligazioni), intellettuali (brevetti), digitali (musica), valuta

**Esempio di supply chain farmaceutica**: R&D → Produzione → Warehouse IN → Trasporto → Warehouse OUT → Healthcare Providers/Retailers → Paziente. Ogni passo è un blocco di transazioni da registrare.

## Definizione e struttura della Blockchain

### Ledger (libro mastro)

Il ledger è il sistema di registrazione per un'azienda: registra il trasferimento di beni tra i partecipanti.

**Distributed Ledger**: archivio di transazioni **append-only** (solo scritture, mai cancellazioni), distribuito e replicato su vari elaboratori.

**Blockchain**: distributed ledger strutturato in una **catena di blocchi** (chain of blocks) collegati:

```
[Blocco 1 Header]   →   [Blocco 2 Header]   →   [Blocco 3 Header]
  Hash Blocco 0           Hash Blocco 1           Hash Blocco 2
  Hash Transazioni 1      Hash Transazioni 2      Hash Transazioni 3
       ↓                       ↓                       ↓
[Transazioni 1..25]    [Transazioni 26..50]    [Transazioni 51..N]
```

- Ogni blocco contiene le transazioni **+ hash del blocco precedente**
- Modificare un blocco → ricalcolare il suo hash → e tutti i successivi → ricreare il consenso su tutti quei blocchi (praticamente impossibile)
- I blocchi validati tramite consenso non vengono mai rimossi

### Esempi di transazioni

- Pagamento della somma X dal conto A al conto B
- Passo di lavorazione/movimentazione in supply chain (tracciabilità)
- Registrazione di certificazione di un lotto alimentare
- Registrazione di un voto in procedimento elettorale
- Aggiunta di un report di analisi cliniche al dossier sanitario

## Il Consenso

Il **consenso** è il processo attraverso cui la maggioranza (o tutti) i nodi validatori si accordano sull'aggiunta di un nuovo blocco.

**Meccanismo base**:
1. Una transazione viene sottomessa a un nodo
2. Il nodo la propaga agli altri nodi (P2P)
3. Tutti i nodi ricevono la transazione, la verificano e possono aggiungerla a un blocco in formazione
4. Il blocco viene validato tramite il meccanismo di consenso scelto

### Meccanismi di consenso

| Meccanismo | Funzionamento | Caratteristiche |
|---|---|---|
| **Proof of Work (PoW)** | I nodi competono risolvendo un problema matematico (SHA-256); chi risolve aggiunge il blocco | Elevato consumo energetico; usato da Bitcoin |
| **Proof of Stake (PoS)** | Il validatore è scelto in base a ricchezza/età del denaro posseduto | Evita computazioni pesanti |
| **Proof of Authority (PoA)** | Transazioni validate da account privilegiati e approvati; sistema di Reputazione | Per blockchain permissioned; più efficiente |

**Perché PoW non è adatto alle blockchain "open"?** Perché il majority vote sarebbe manipolabile da chi controlla più del 50% della potenza di calcolo.

## Perché usare Blockchain vs. database centralizzato?

| Vantaggio BC | Motivazione |
|---|---|
| **Sicurezza crittografica** | Crittografia avanzata "best of the breed" |
| **Robustezza** | Ledger distribuito e duplicato: perdita di una copia non crea problemi |
| **Storia verificabile** | BC contiene l'intera storia delle transazioni, verificabile da tutti |
| **Integrità del processo** | Autenticare una transazione richiede controllo da parte della maggioranza dei nodi |

**Trade-off**: BC è **meno efficiente e meno veloce** di un DB centrale ma è **molto più robusta** rispetto a manipolazioni.

## Tipi di Blockchain

### Libere (Unpermissioned / Pubbliche)
- Esempi: Bitcoin, Ethereum, Litecoin
- Aperte, nessuna "proprietà", non controllate
- Ogni partecipante può contribuire agli aggiornamenti del ledger
- I partecipanti sono **anonimi**

### Controllate / Consorziate (Permissioned)
- Esempi: R3 (banche), EWF (energia), B3i (assicurazioni), **Hyperledger Fabric**
- Gestite da un consorzio; partner devono essere autorizzati
- Approvazione limitata a partecipanti "affidabili" e **non anonimi**
- Adatte a: istituzioni, grandi imprese con filiere complesse, banche, assicurazioni

### Private
- Esempi: MONAX, Multichain
- Gestite centralmente da una sola organizzazione

## Smart Contract

Un **Smart Contract** è un programma informatico memorizzato sulla blockchain (quindi non alterabile) che viene eseguito da più nodi della rete a partire dagli stessi input per validare l'output. Consente di automatizzare l'esecuzione di contratti condizionali senza intermediari.

## Proprietà delle Blockchain

| Proprietà | Descrizione |
|---|---|
| **Verificabilità pubblica** | Ogni partecipante può verificare i contenuti della BC |
| **Robustezza** | Se un nodo si danneggia, tutti gli altri rimangono operativi |
| **Irrevocabilità** | Transazioni immutabili una volta confermate |
| **Trasparenza** | Transazioni visibili a tutti i partecipanti |
| **Tracciabilità** | Possibile ricostruire la sequenza di transazioni che hanno trattato un asset |
| **Privacy** | L'identità dei partecipanti può essere resa anonima o pseudo-anonima |
| **Automazione** | Una BC è programmabile (tramite Smart Contract) |

## Limiti delle Blockchain

- Mancanza di scalabilità
- Prestazioni limitate
- Problemi di interoperabilità
- Mancanza di sostenibilità (PoW ad alto consumo energetico)
- Inquadramento legale degli smart contract

## Applicazioni nei SI aziendali

- **Supply chain** (es. farmaceutico, agroalimentare): tracciabilità prodotti dalla produzione al consumatore
- **Pagamenti inter-organizzativi**: eliminazione intermediari finanziari
- **Certificazioni**: registrazione immodificabile di qualità, conformità, brevetti
- **Healthcare**: dossier sanitari distribuiti

**Quiz - risposta corretta**: per un consorzio agroalimentare con 10.000 transazioni/giorno tra attori autorizzati → blockchain **permissioned con Proof of Authority** (C), perché permette validazione rapida da parte di attori con identità verificata, senza l'overhead del PoW.

## Articoli correlati

- [[erp-evoluzione]]
- [[erp]]
- [[tipologie-si]]
- [[introduzione-si]]

## Fonti

- raw/L-BLOCKCHAIN-SI.pdf
