---
tags: [enterprise, cluster, hci, distribuiti, integrazione, iac, iam, monitoring, amministrazione-di-sistema]
data_creazione: 2026-05-28
data_aggiornamento: 2026-05-28
fonti:
  - raw/4 - SistemiEnterprise_COMPILED.pdf
---

# Sistemi Enterprise e Integrazione

I **sistemi enterprise** sono grandi sistemi informatici (mainframe, cluster, sistemi distribuiti ed eterogenei) che supportano il business critico di un'organizzazione. La loro gestione richiede strategie di consolidamento, interoperabilità tra piattaforme diverse e strumenti centralizzati per configurazione, monitoraggio, identità e aggiornamenti.

## Punti chiave

- **Sistema enterprise** = hardware + software di grandi dimensioni, mission-critical, spesso eterogeneo (vendor e OS diversi).
- **Sistemi legacy**: mission-critical, enormi, costosi, non documentati; approcci: ignorare, sostituire, integrare in sistemi distribuiti.
- **Legge di Amdahl**: lo speedup di un programma parallelo è limitato dalla frazione sequenziale — spiega perché aggiungere processori ha rendimenti decrescenti.
- **Teorema CAP** (Brewer): un sistema distribuito può garantire al massimo 2 delle 3 proprietà: Consistency, Availability, Partition Tolerance.
- **HCI (Hyper-Converged Infrastructure)**: server + storage + rete integrati in un unico nodo; semplifica il deployment e riduce la complessità.
- **Infrastructure as Code (IaC)**: gestione dichiarativa e idempotente dell'infrastruttura tramite codice (Ansible, Puppet, Terraform).
- **IAM (Identity and Access Management)**: gestione centralizzata di identità, autenticazione (MFA), autorizzazione (RBAC) e SSO.

---

## Sistemi Enterprise

**Definizione**: in informatica, "enterprise" indica sistemi di grandi dimensioni — mainframe, mini, cluster paralleli, sistemi distribuiti eterogenei.

### Sistemi Legacy

| Caratteristica | Dettaglio |
|---------------|-----------|
| **Definizione** | Sistema informativo critico per il business, ereditato dal passato |
| **Platform** | Mainframe, SPARC; software: COBOL, PL/1, DB2 |
| **Operatività** | Mission-critical, 100% uptime, 7/7 |
| **Dimensione** | Milioni di linee di codice, non documentato |
| **Approcci di gestione** | (1) Ignorare il problema; (2) sostituzione netta; (3) integrazione in sistema distribuito |

### High Density Server

Server ottimizzati per spazio e consumo energetico a parità di potenza:
- **Rack 1U**: 1 rack unit = 44,45 mm; 1 rack standard = 19" = 42U.
- **Blade server**: chassis da 7U che ospita 10-14 blade; condivisione di alimentazione, raffreddamento e networking.

### Consolidamento

| Tipo | Descrizione | Nota |
|------|-------------|------|
| **Physical consolidation** | Rack / blade | Risparmia spazio, non migliora l'utilizzo |
| **Application consolidation** | Più app sullo stesso server | Rischio di conflitti e resource contention |
| **Virtual infrastructure** | VM su hypervisor | Massimizza utilizzo, ROI e manageability |
| **Data/Storage consolidation** | NAS, SAN centralizzati | Ottimizza utilizzo disco/DB |

---

## Sistemi Paralleli e Distribuiti

### Tassonomia di Flynn

| Categoria | Descrizione | Esempi |
|-----------|-------------|--------|
| **SISD** | Single Instruction, Single Data | CPU classica von Neumann |
| **SIMD** | Single Instruction, Multiple Data | Intel SSE, AMD 3DNow!, GPU |
| **SIMT** | Single Instruction, Multiple Thread | NVIDIA CUDA |
| **MISD** | Multiple Instruction, Single Data | Rarissimo (ridondanza fault-tolerant) |
| **MIMD** | Multiple Instruction, Multiple Data | Multiprocessore (UMA/NUMA), Multicomputer (MPP, COW) |

### Legge di Amdahl

Lo speedup massimo con N processori è:

```
Speedup = 1 / ((1 - P) + P/N)
```

Dove P è la frazione parallelizzabile del programma. Se P = 0,95 e N = 10 → speedup ≈ 6,9 (non 10). La parte sequenziale è il collo di bottiglia.

### Sistemi Cluster

| Tipo | Funzione |
|------|---------|
| **HA / Fail-over** | Ridondanza: se un nodo cade, un altro prende il carico |
| **Load Balancing** | Distribuisce richieste tra più nodi per performance |
| **HPC (High-Performance Computing)** | Calcolo scientifico/parallelo massiccio |
| **Grid Computing** | Risorse distribuite geograficamente, loose coupling |

### Infrastruttura convergente e iperconvergente

```
Traditional:         Converged:              Hyper-Converged:
Server               Server                  Storage+Server
Storage Switch    →  Network+Storage     →   (tutto integrato)
HA Shared Storage    Switch                  in pochi nodi
```

- **CI (Converged Infrastructure)**: server + storage + rete pre-integrati; riduce il cabling ma non elimina i silos.
- **HCI (Hyper-Converged Infrastructure)**: software-defined storage distribuito sui nodi server (es. VMware vSAN, Nutanix); nessun storage esterno dedicato.
- **CDI (Composable Disaggregated Infrastructure)**: "il datacenter come computer" — risorse (CPU, GPU, storage, memoria) disaggregate e composte on-demand via fabric (Gen-Z, CXL).

### Teorema CAP

Un sistema distribuito può garantire al massimo 2 di 3:

| Proprietà | Descrizione |
|-----------|-------------|
| **Consistency (C)** | Tutti i nodi vedono gli stessi dati nello stesso momento |
| **Availability (A)** | Ogni richiesta riceve una risposta (successo o errore) |
| **Partition Tolerance (P)** | Il sistema funziona nonostante perdita di messaggi tra nodi |

- **CP**: consistenza + tolleranza partizioni → MongoDB, HBase, Redis.
- **AP**: disponibilità + tolleranza partizioni → Cassandra, CouchDB, RIAK.
- **CA**: consistenza + disponibilità → RDBMS classici (Oracle, MySQL) — non tollerano partizioni.

---

## Heterogeneous Compute e xPU

I workload moderni (ML, 5G, cloud-native) richiedono acceleratori oltre la CPU:

| Acronimo | Nome | Uso |
|----------|------|-----|
| **GPU** | Graphics Processing Unit | Calcolo parallelo massiccio (ML, rendering) |
| **NPU / TPU** | Neural/Tensor Processing Unit | Inferenza e training reti neurali |
| **DPU** | Data Processing Unit | Offload network/storage dal CPU |
| **FPGA** | Field Programmable Gate Array | Logica configurabile, bassa latenza |
| **ASIC** | Application-Specific IC | Funzione fissa, massima efficienza |
| **SmartNIC** | NIC intelligente | Network+storage processing sulla scheda |
| **PIM** | Processing in Memory | Calcolo vicino alla memoria (riduce data movement) |

**Bottleneck CPU/GPU**: la comunicazione tra la memoria di sistema (DRAM CPU) e la Device Memory (DRAM GPU) è il collo di bottiglia principale per applicazioni non-grafiche.

---

## Integrazione di Sistemi Eterogenei

### Standard e protocolli

**Organismi di standardizzazione**: ISO, IEEE, IEC, ITU (ex CCITT), SNIA.

**Standard chiave**:

| Dominio | Standard |
|---------|---------|
| Unix/POSIX | IEEE 1003.x, ISO 9945 (interfacce OS, thread, socket, sicurezza) |
| TCP/IP | RFC (www.rfc-editor.org) |
| Directory | LDAP (RFC 4511) |
| Network Management | SNMP, IPMI, WBEM, CIM, DMTF Redfish |
| Server Management | SMBIOS, DMI, SMASH |
| Data interchange | REST+JSON, OData, XML |

### Protocolli Unix

| Protocollo | Funzione | Note |
|-----------|---------|------|
| **NIS** (Network Information System) | Directory centralizzata utenti/host | Introdotto da Sun negli anni '80; file esportabili: passwd, group, hosts, aliases |
| **NFS** (Network File System) | Condivisione filesystem via rete | Bind degli UID; config `/etc/exports`; Windows richiede client apposito |
| **LPR/LPD** | Stampa in rete Unix | |
| **LDAP** | Directory service generico | Struttura più flessibile di NIS; base di AD |

**Stack NIS/NFS**: Livelli 1-2 → IP → TCP/UDP → RPC → XDR → NFS/NIS.

### Protocolli Microsoft

**SMB / CIFS** (Server Message Block / Common Internet File System):
- UNC: `\\SERVER\SHARE`
- Login: `DOMINIO\LOGIN` o `login@dominio` (UPN, User Principal Name)
- Trasporto: NetBEUI Frame (NBF) o NetBIOS su TCP/IP (NetBT)
- Interfacce: NetBIOS 3.0, TDI, NDIS

### Formati dati strutturati

| Formato | Caratteristiche | Vantaggi | Svantaggi |
|---------|----------------|---------|-----------|
| **JSON** | Testuale, leggibile dagli umani | Semplice, supporto scripting | Nessun schema formale |
| **YAML** | Superset di JSON 1.2 | Commenti, tipi estensibili, più leggibile | Sensibile all'indentazione |
| **XML** | Tag apertura/chiusura | Schema formale (XSD), SOAP | Verboso, overhead spazio |

### CLI e automazione

**Shell native**: Linux → Bash; Windows → CMD (poi PowerShell da NT 6.0).

**PowerShell Core**: multipiattaforma (Windows, Linux, macOS); espandibile tramite moduli.

**CLI universali**: bash (multi-OS), PowerShell Core, Python, Perl, Ruby.

**Oltre la CLI**: approccio dichiarativo (idempotente) vs. procedurale; RESTful API (`curl`, `Invoke-RestMethod`); Infrastructure as Code.

---

## Gestione di Sistemi Enterprise

### Organizzazione e inventario

- Definire standard interni (naming convention, procedura installazione, policy password).
- **Inventario asset**: hardware (lshw, msinfo32), software (rpm -qa), licenze, configurazioni.
- Documentazione come prerequisito per qualsiasi altra gestione.

### Gestione centralizzata — Enterprise Management Platform (EMP)

Sistema multi-tier che affronta la complessità di ambienti eterogenei (vendor, OS, applicazioni, protocolli diversi).

**Funzionalità richieste**:

| Funzionalità | Descrizione |
|-------------|-------------|
| Configuration Management | Gestione configurazioni sistemi |
| Asset/IPAM | Inventario IP e hardware |
| Patch Management | Distribuzione aggiornamenti centralizzata |
| Monitoring | Health, availability, performance, security |
| Log Centralizzati | Raccolta e analisi log da fonti eterogenee |
| Remote Access | Console centralizzata (KVM switch, HP iLO, Dell DRAC, RDP, SSH) |

### IPAM (IP Address Management)

Gestione pianificata degli indirizzi IP e risorse di rete correlate:
- **Open source**: RackTable.
- **Commerciale**: SolarWinds IP Address Manager, Windows Server IPAM integrato.

### Infrastructure as Code (IaC)

Gestione dichiarativa e idempotente dell'infrastruttura tramite codice versionato in Git.

| Metodo | Descrizione |
|--------|-------------|
| **Push** | Il server centrale spinge le configurazioni ai nodi |
| **Pull** | I nodi interrogano periodicamente il server per ottenere la configurazione desiderata |

| Tool | Metodo | Linguaggio config | Note |
|------|--------|------------------|------|
| **Ansible** | Push | YAML (playbook) | Agentless, SSH |
| **Puppet** | Pull | Puppet DSL (dichiarativo) | Agent su ogni nodo |
| **Chef** | Pull | Ruby (cookbook) | Altamente flessibile |
| **SaltStack** | Push/Pull | YAML + Jinja2 | Veloce su larga scala |
| **Terraform** | Push | HCL (dichiarativo) | Specializzato su cloud/IaC |

**Maturità delle operazioni cloud**:
```
Script (bash/PS) → Config Management → IaC → GitOps
                   Chef/Puppet/Ansible   Terraform/ARM   Git+CI/CD
```

### Gestione aggiornamenti

>21.957 vulnerabilità pubblicate nel solo 2021 (+60/giorno).

| Tipo aggiornamento | Soluzioni |
|-------------------|-----------|
| OS Windows | WSUS, SCCM |
| OS Linux | Repository DEB/RPM locali, proxy con cache |
| Antivirus | Push da console centrale o pull da sito interno |
| Applicazioni | Package manager, deploy automatico |

### Gestione del ciclo di vita (Lifecycle Management)

Fasi: **provisioning → configurazione → manutenzione → dismissione**.
- Open source: The Foreman.
- Proprietario: vendor-specific tools.

### Migrazione — Le 7R (AWS)

| Strategia | Descrizione | Effort |
|-----------|-------------|--------|
| **Refactor** | Riprogetta cloud-native | Massimo |
| **Replatform** | Piccole ottimizzazioni cloud | Alto |
| **Repurchase** | Cambia prodotto (es. SaaS) | Medio |
| **Rehost** (Lift & Shift) | Sposta as-is | Basso |
| **Relocate** | Sposta su infrastructure diversa | Basso |
| **Retain** | Mantieni on-premises per ora | Nessuno |
| **Retire** | Dismetti l'applicazione | Nessuno |

---

## Identity and Access Management (IAM)

### AAA

| Componente | Funzione |
|-----------|---------|
| **Authentication** | Verifica l'identità ("chi sei?") |
| **Authorization** | Determina cosa può fare l'utente |
| **Accounting** | Traccia le azioni degli utenti |

**LAN AAA**: Radius.
**Web AAA**: SAML, OAuth 2.0 (bearer token), OpenID Connect.

### Modalità di autenticazione

| Fattore | Esempi |
|---------|--------|
| **Cosa conosco** | Password, OTP (One Time Password) |
| **Cosa possiedo** | Token hardware, certificato digitale, smartphone |
| **Cosa sono** | Fingerprint, riconoscimento facciale |

**MFA (Multifactor Authentication)**: combina ≥2 fattori. Affronta il 99,9% degli attacchi all'identità. Tipologie: SMS, app authenticator (TOTP), hard token, FIDO2/WebAuthn (non phishable).

**Passwordless**: autenticazione senza password (Windows Hello, biometria, chiavi hardware FIDO2).

### Autorizzazione

- **RBAC** (Role-Based Access Control): Ruoli + Soggetti + Oggetti = Permessi effettivi.
- **Four-eyes principle**: azioni critiche richiedono l'approvazione di due persone distinte.
- **Delegazione e revoca**: i permessi possono essere assegnati e ritirati senza intervento sul sistema target.

### SSO (Single Sign-On)

| Tipo | Protocolli |
|------|-----------|
| **LAN SSO** | Kerberos (vero SSO), NTLM (non è vero SSO) |
| **Web SSO** | OpenID Connect, OAuth 2.0, SAML, Passport/LiveID |

### Certificati e PKI

- **PKI** (Public Key Infrastructure): CA pubblica o privata (OpenCA, Windows Server CA).
- **CRL** (Certificate Revocation List): lista dei certificati revocati; OCSP è l'alternativa più efficiente (query real-time).
- **KMIP** (Key Management Interoperability Protocol): standard SNIA per gestione centralizzata chiavi simmetriche/asimmetriche.

### Identità digitale in Italia

SPID, Carta Nazionale dei Servizi (CNS), Carta Regionale dei Servizi (CRS), nuova carta d'identità elettronica, passaporto elettronico.

---

## Monitoraggio e Log Centralizzati

### Monitoraggio

| Tipo | Funzione |
|------|---------|
| **Health monitoring** | Rileva guasti hardware/software |
| **Availability monitoring** | Misura l'uptime dei servizi |
| **Performance/Capacity monitoring** | Identifica colli di bottiglia e trend di crescita |
| **Security monitoring** | Rileva comportamenti anomali |

**Protocolli**: SNMP (trap per allarmi, MIB per dati), IPMI (gestione hardware out-of-band).

**Tool open source**: Nagios, OpenNMS, Cacti, Ganglia, Zenoss, MRTG/RRDtool.
**Tool proprietari**: HP OpenView, PRTG, Microsoft MOM/SCOM.

### Log centralizzati

**Standard formato**: Syslog, Common Event Format (CEF), Log Event Extended Format (LEEF).

**Raccolta**: Syslog-NG, Fluentd, Logstash; **analisi**: Splunk, ELK stack.

**Architettura Fluentd**: M×N connessioni → M+N (ogni sorgente manda a un aggregatore, non a ogni destinazione).

### SIEM (Security Information and Event Management)

Combina **SIM** (security information management) + **SEM** (security event management):
- Log collection, log analysis, event correlation, log forensics.
- User activity monitoring, IT compliance, real-time alerting.
- Integrazione moderna con Big Data e AI/ML.

### Detection & Response

| Acronimo | Significato | Scope |
|----------|------------|-------|
| **EDR** | Endpoint Detection and Response | Singolo endpoint |
| **NDR** | Network Detection and Response | Traffico di rete |
| **XDR** | Extended Detection and Response | Multi-layer (endpoint+rete+cloud) con AI |
| **MDR** | Managed Detection and Response | Servizio gestito da terzi |

---

## Risorse Centralizzate

### Condivisione risorse

| Risorsa | Protocolli |
|---------|-----------|
| File | SMB (Windows), NFS (Unix) |
| Stampanti | SMB, LPR/LPD |
| Database | ODBC + ANSI SQL |
| Utenti | Active Directory / NIS / LDAP |

### Installazione centralizzata

- **Linux**: PXE boot + kickstart/preseed; sorgenti via HTTP, FTP o NFS.
- **Windows**: WDS (Windows Deployment Services), SCCM; kit OEM (OPK).

### Gestione licenze

- **Licenze standalone**: inventariarle per compliance.
- **Volume licensing Microsoft**: MAK (Multiple Activation Key) o KMS (Key Management Services) per attivazione interna.

### Gestione dispositivi mobili

| Soluzione | Acronimo | Funzione |
|---------|---------|---------|
| Mobile Device Management | MDM | Gestisce l'intero dispositivo (config, compliance, wipe) |
| Mobile Application Management | MAM | Gestisce solo le app aziendali sul dispositivo |
| Endpoint Management Platform | EMP | Piattaforma unificata (identità + app + desktop + mobile) |

---

## Articoli correlati

- [[cloud-computing]]
- [[virtualizzazione]]
- [[linux-utenti-autenticazione]]
- [[linux-log-monitoraggio-rete]]
- [[server-windows]]
- [[servizi-infrastrutturali]]
- [[storage-server]]

## Fonti

- `raw/4 - SistemiEnterprise_COMPILED.pdf` — Slide del corso Amministrazione di Sistema, UniBS, a.a. 2024-25
