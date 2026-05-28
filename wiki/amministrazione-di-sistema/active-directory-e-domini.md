---
tags: [active-directory, ldap, kerberos, windows, domini, samba, amministrazione-di-sistema]
data_creazione: 2026-05-28
data_aggiornamento: 2026-05-28
fonti:
  - raw/5 - SoluzioniEnterprise.pdf
---

# Active Directory e Domini

**Active Directory (AD)** è il sistema integrato e distribuito di directory service Microsoft, introdotto con Windows 2000 (beta 1996). Gestisce l'identità e l'accesso centralizzato in una rete Windows, integrando standard aperti (DNS, LDAP, Kerberos, X.500) con estensioni proprietarie.

## Punti chiave

- **Architettura gerarchica**: oggetti organizzati in Dominio → Tree → Forest; ogni livello ha trust automatici bidirezionali e transitivi.
- **Domain Controller (DC)**: server Windows con AD installato; contiene il database NTDS (file `Ntds.dit` in `C:\Windows\NTDS`) e la share SYSVOL.
- **Autenticazione**: Kerberos V5 (default) o NTLM (legacy); ogni DC funge anche da KDC.
- **FSMO**: 5 ruoli Single-Master (Schema Master, Domain Naming Master, PDC Emulator, RID Master, Infrastructure Master) su cui si concentrano operazioni critiche.
- **GPO**: Group Policy Objects applicati in ordine Site → Domain → OU; strumento principale di gestione centralizzata di workstation e server.
- **Replica**: intra-sito continua e non compressa; inter-sito discreta e compressa (configurabile via Site Link).

---

## Domini e struttura logica

| Termine | Descrizione |
|---------|-------------|
| **Dominio** | Contenitore di oggetti; almeno un DC; unità amministrativa e di sicurezza |
| **Root Domain** | Dominio radice della gerarchia |
| **Tree** | Insieme di domini con namespace DNS contiguo |
| **Forest** | Insieme di uno o più tree; confine di sicurezza massimo |
| **OU (Organizational Unit)** | Sotto-contenitore nel dominio; ambito delle GPO e della delega amministrativa |
| **Sito** | Raccordo tra struttura logica AD e rete fisica; delimita il contesto di replica |

**Trust tra foreste**: bidirezionale e transitivo all'interno di una foresta; esterno one-way non-transitivo verso domini NT legacy.

---

## Active Directory — tecnologie integrate

AD integra i seguenti standard:

| Standard | Utilizzo in AD |
|----------|---------------|
| **DNS** | Discovery di DC, GC, Kerberos tramite record SRV; sostituisce WINS e broadcast |
| **LDAP** | Protocollo di comunicazione con la directory; porta 389 (636 LDAPS) |
| **X.500** | Modello dati per elencare oggetti |
| **Kerberos V5** | Autenticazione di default |
| **X.509 (PKI)** | Certificati digitali |
| **DHCP / SNTP** | Servizi integrati nella gestione di sito |
| **LDIF** | Formato per importazione/esportazione bulk |

---

## Database e file critici

- **NTDS.dit**: database AD (`C:\Windows\NTDS\ntds.dit`); contiene tutti gli oggetti.
- **SYSVOL** (`C:\Windows\SYSVOL`): share replicata su tutti i DC; contiene script di logon e oggetti GPO.
- **Partizioni AD**: Schema (forest-wide), Configuration (forest-wide), Domain (domain-wide), Application (configurabile; include ForestDNSZone / DomainDNSZone).

---

## Ruoli FSMO

| Livello | Ruolo | Funzione |
|---------|-------|---------|
| Forest | **Schema Master** | Unico autorizzato a modificare lo schema |
| Forest | **Domain Naming Master** | Aggiunge/rimuove domini dalla forest |
| Domain | **PDC Emulator** | Sincronizzazione orario, autenticazione legacy, cambio password |
| Domain | **RID Master** | Distribuisce pool di RID ai DC per la creazione di SID |
| Domain | **Infrastructure Master** | Mantiene riferimenti a oggetti di altri domini |

Comandi: `netdom query fsmo` per elencarli, `Ntdsutil.exe` per spostarli.

---

## Global Catalog e replica

**Global Catalog (GC)**: ruolo speciale di un DC che mantiene una copia parziale di tutti gli oggetti della forest. Necessario per:
- Trovare oggetti nell'intera forest.
- Login con User Principal Name (UPN).
- Supporto ai gruppi universali.

**Replica AD**:
- **Intra-sito**: continua, non compressa (LAN veloce).
- **Inter-sito**: schedulata, compressa, tramite Site Link.
- **Read-Only DC (RODC)**: introdotto in 2008, riceve replica ma non scrive; utile per filiali con bassa sicurezza fisica.

---

## Naming Convention

| Sistema | Formato | Esempio |
|---------|---------|---------|
| LDAP Distinguished Name | `CN=nome, CN=container, DC=dominio, DC=tld` | `CN=Jeff Smith, CN=Users, DC=contoso, DC=msft` |
| LDAP Relative DN | Solo il componente foglia | `CN=Jeff Smith` |
| UPN (Kerberos) | `utente@dominio` | `JeffS@contoso.msft` |
| SPN | `servizio/host@realm` | `HTTP/web01.contoso.msft` |
| GUID | Identificatore univoco globale | `{xxxxxxxx-...}` |
| UNC | `\\server\share` | `\\dc01\SYSVOL` |

---

## Group Policy (GPO)

**Group Policy Object**: oggetti che applicano configurazioni a utenti e computer. Tipi principali:

| Categoria | Contenuto |
|-----------|-----------|
| Administrative Templates | Impostazioni registro |
| Security | Policy locali, di dominio, di rete |
| Software Installation | Distribuzione centralizzata software |
| Scripts | Startup, shutdown, logon, logoff |
| Folder Redirection | Redirect home/desktop su server |

**Ordine di applicazione** (dal più generale al più specifico, l'ultimo vince):
1. Local GPO
2. Site GPO
3. Domain GPO
4. OU GPO (dalla OU padre alla foglia)

Strumento di debug: **RSOP (Resultant Set of Policy)** snap-in.

---

## AD nel cloud

| Soluzione | Descrizione |
|-----------|-------------|
| AD DC su IaaS | VM Windows Server con ruolo DC in cloud (qualsiasi provider) |
| **Azure AD DS** | DC as a Service su Azure (managed); supporta LDAP, Kerberos, NTLM |
| **Microsoft Entra ID** | Ex Azure AD (rinominato luglio 2023); identity provider cloud per app web e API; usa OAuth 2.0, OpenID Connect, WS-Federation, SAML 2.0 |

**Scenari di autenticazione ibrida** (on-prem + Entra ID):

| Scenario | Descrizione |
|----------|-------------|
| Cloud user | Identità solo cloud, indipendente |
| Password Hash Sync | Hash password sincronizzato su Entra ID |
| Pass-through Authentication | Entra ID Connect gestisce autenticazione verso AD on-prem |
| Federation | ADFS on-premises; Entra ID reindirizza autenticazione |

---

## LDAP — struttura e funzionamento

**Directory Information Tree (DIT)**: albero gerarchico di entry. Ogni entry è identificata da:
- **DN (Distinguished Name)**: percorso univoco dall'oggetto alla radice.
- **Attributi**: coppie `tipo=valore` (es. `cn=Barbara Jensen`).

Implementazioni: Microsoft AD, OpenLDAP, IBM Directory Server, Oracle Directory Service.

---

## Kerberos

Protocollo di autenticazione distribuito (MIT, 1983). Basato su **mutual authentication** e **Trusted Third Party (KDC)**.

**Componenti**:
- **AS (Authentication Service)**: verifica password, rilascia TGT (Ticket-Granting Ticket).
- **TGS (Ticket-Granting Service)**: rilascia Service Ticket per accedere ai servizi.
- **Realm**: dominio di amministrazione Kerberos (convenzionalmente il nome DNS in MAIUSCOLO).

**Flusso** (6 passi):
1. Client → AS: richiesta TGT
2. AS → Client: TGT cifrato con chiave utente
3. Client → TGS: richiesta Service Ticket con TGT
4. TGS → Client: Service Ticket
5. Client → Server: Service Ticket
6. Server → Client: autenticazione reciproca completata

**Vantaggi vs NTLM**:
- Autenticazione più efficiente (server verifica il ticket localmente senza contattare il DC ad ogni richiesta).
- Mutual authentication (NTLM non la supporta).
- Trust transitivo tra domini Windows.

Implementazioni Linux: MIT Kerberos V (krb5), Heimdal (SuSE).

---

## RADIUS e TACACS+

Protocolli AAA (Authentication, Authorization, Accounting) nati per router e apparati di rete:
- **RADIUS** (Remote Authentication Dial In User Service): standard IETF; usato anche per AP Wi-Fi; implementazione Linux: FreeRADIUS.
- **TACACS+** (Terminal Access Controller Access-Control System): orientato alla gestione di apparati Cisco.

Implementazioni Windows: IAS (Internet Authentication Service, 2003), NPS (Network Policy Server, versioni successive).

---

## Condivisione risorse: DFS e Samba

### DFS (Distributed File System)
- **Namespace unificato** per share distribuite su più server: `\\dominio\rootDFS\share`.
- **DFS-R** (Distributed File System Replication): replica multi-master con compressione delta (RDC); sostituisce FRS da Windows 2008.
- Richiede Windows Server con ruolo DFS installato.

### Samba
Implementazione open-source dei protocolli SMB/CIFS per Linux/Unix:
- **Client**: `smbmount`, `smbclient`.
- **Server**: file server, print server, PDC, script logon, gestione profili.
- Le macchine Unix comunicano usando protocolli nativi Microsoft (nessun client aggiuntivo sul lato Windows).

---

## Articoli correlati

- [[server-windows]]
- [[linux-utenti-autenticazione]]
- [[sistemi-enterprise-e-integrazione]]
- [[servizi-infrastrutturali]]
- [[dns-server]]
- [[cloud-computing]]

## Fonti

- `raw/5 - SoluzioniEnterprise.pdf` — Slide del corso Amministrazione di Sistema, UniBS, a.a. 2024-25
