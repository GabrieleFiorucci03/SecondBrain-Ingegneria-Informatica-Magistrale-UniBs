---
tags: [servizi, infrastruttura, workload, itil, amministrazione-di-sistema]
data_creazione: 2026-05-28
data_aggiornamento: 2026-05-28
fonti:
  - raw/3 - Servizi_COMPILED.pdf
---

# Servizi Infrastrutturali

Un **servizio** (definizione ITIL) è un mezzo per consegnare valore ai clienti facilitando gli outcome desiderati, senza che il cliente si faccia carico dei costi e rischi specifici. Un servizio può essere composto da più sistemi e servizi interdipendenti (non è un concetto puramente tecnologico).

## Punti chiave

- **Servizi vs sistemi**: un singolo servizio (es. posta elettronica) attraversa più strati — storage, rete, server applicativo, directory, client — ognuno con i propri requisiti.
- **Workload**: in ambienti enterprise si identifica tipicamente 1 servizio = 1 workload = 1 server; è fondamentale classificare il tipo di carico per dimensionare correttamente l'hardware.
- **Dipendenze upstream/downstream**: quando B dipende da A (A ← B), A è upstream; la catena di dipendenze determina il propagarsi dei fault.
- **Caratteristiche fondamentali**: reliability, stability, availability, recoverability, scalability, performance, manageability, security, integrity, data protection, data sovereignty, observability.
- **Modalità di implementazione**: on-premises (fisico, virtuale, container) o cloud pubblico (IaaS, PaaS, SaaS).
- **Servizi infrastrutturali** abilitano tutti gli altri servizi (NTP, DHCP, DNS, AD, backup, logging, monitoraggio, SSH).

---

## Classificazione dei workload

### Per tipo di elaborazione (sistemi informativi)

| Tipo | Descrizione | Caratteristica dominante |
|------|-------------|--------------------------|
| **OLTP** (Online Transaction Processing) | Transazioni brevi, frequenti, in scrittura | Bassa latenza, alta concorrenza, RDBMS normalizzato |
| **OLAP** (Online Analytical Processing) | Query analitiche su grandi dataset storici | Banda, CPU, data warehouse denormalizzato |

### Per risorsa dominante (tecnico)

| Tipo | Workload tipici | Hardware critico |
|------|----------------|-----------------|
| **CPU intensive** | HPC, VDI, OLTP | CPU core count, frequenza |
| **Memory intensive** | BPI (Business Processing), VDI, virtualizzazione | RAM capacity, bandwidth |
| **I/O intensive** | Business processing, UC&C, Big Data | Storage IOPS, latenza, bandwidth |
| **Network intensive** | VDI, HPC, OLTP | Bandwidth NIC, latenza rete |

### Workload comuni e requisiti hardware prevalenti

| Workload | Memoria | CPU | Storage | Rete | Rack density |
|----------|---------|-----|---------|------|--------------|
| Server virtualization | Alta | Alta | Media | Bassa | Alta |
| Business processing/OLTP | Alta | Alta | Media | Media | Bassa |
| VDI | Alta | Alta | Alta | Alta | Media |
| HPC | Media | Alta | Bassa | Alta | Alta |
| UC&C | Media | Media | Alta | Alta | Media |
| Big data/analytics | Media | Media | Alta | Media | Media |

**Dimensionamento**: scegliere se progettare per i picchi o per i valori medi dipende dal contesto; il cloud favorisce l'elasticità per i picchi.

---

## Dipendenze tra servizi

La notazione `A ← B` indica che B **dipende da** A (A è upstream rispetto a B):

```
[DNS] ← [Active Directory] ← [Mail Server] ← [Client]
```

Un fault upstream si propaga a tutti i downstream. L'analisi delle dipendenze è essenziale per:
- Calcolare l'availability effettiva del servizio end-to-end.
- Pianificare le finestre di manutenzione senza impatti a cascata.
- Dimensionare correttamente la ridondanza.

---

## Servizi infrastrutturali principali

| Servizio | Funzione |
|----------|----------|
| **NTP Server** | Sincronizzazione dell'orologio di sistema — prerequisito per log, Kerberos, certificati |
| **DHCP Server** | Assegnazione dinamica degli indirizzi IP e dei parametri di rete |
| **DNS Server** | Risoluzione nomi ↔ IP — servizio critico su cui dipende quasi tutto |
| **Active Directory / LDAP** | Directory service per autenticazione e autorizzazione centralizzata |
| **Backup** | Protezione dei dati contro perdita e corruzione |
| **Log / Syslog** | Centralizzazione e conservazione dei log di sistema e applicativi |
| **Monitoraggio / SNMP** | Visibilità proattiva su disponibilità e performance |
| **SSH** | Accesso remoto sicuro ai sistemi |

---

## Servizi applicativi principali

| Servizio | Esempi |
|----------|--------|
| **DBMS** | PostgreSQL, MySQL, Oracle, SQL Server |
| **ERP** | SAP, Microsoft Dynamics |
| **CMS / Web App** | WordPress, Drupal, applicazioni custom |
| **Mail** | Postfix + Dovecot, Exchange, Microsoft 365, Google Workspace |
| **File Server / Archiving** | Samba, SharePoint, NFS |

---

## Modalità di implementazione

| Modalità | Varianti | Note |
|----------|----------|------|
| **On-premises fisico** | Server dedicato | Massimo controllo, costi CapEx |
| **On-premises virtuale** | VM su hypervisor | Consolidamento, isolamento, snapshot |
| **Container** | Docker, Kubernetes | Densità elevata, portabilità, avvio rapido |
| **IaaS** (cloud) | Azure VM, AWS EC2 | Hardware gestito dal provider |
| **PaaS** (cloud) | Azure App Service, AWS RDS | Piattaforma gestita, focus sul codice |
| **SaaS** (cloud) | Microsoft 365, Google Workspace | Nessuna gestione infrastrutturale |

---

## Articoli correlati

- [[ntp-e-dhcp]]
- [[dns-server]]
- [[web-server]]
- [[mail-server]]
- [[ftp-server]]
- [[server]]
- [[linux-log-monitoraggio-rete]]
- [[server-windows]]

## Fonti

- `raw/3 - Servizi_COMPILED.pdf` — Slide del corso Amministrazione di Sistema, UniBS, a.a. 2024-25
