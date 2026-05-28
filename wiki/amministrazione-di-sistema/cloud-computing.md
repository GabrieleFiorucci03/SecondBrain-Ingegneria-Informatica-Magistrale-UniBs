---
tags: [cloud, iaas, paas, saas, aws, azure, gcp, amministrazione-di-sistema]
data_creazione: 2026-05-28
data_aggiornamento: 2026-05-28
fonti:
  - raw/4 - CloudComputing_COMPILED.pdf
---

# Cloud Computing

Il **cloud computing** è un insieme di tecnologie informatiche che permettono l'utilizzo di risorse (storage, CPU, rete) distribuite, erogate come servizio on-demand, con self-provisioning, pagamento a consumo (pay-per-use) e scalabilità elastica. Non è una destinazione fisica ma un modello operativo — sintetizzato nell'acronimo **AAA**: Astratto, Agile, Automatico.

## Punti chiave

- **Definizione NIST**: 5 caratteristiche essenziali — On-demand self-service, Broad network access, Resource pooling (multi-tenant), Rapid elasticity, Measured service.
- **Modelli di delivery**: IaaS (infrastruttura), PaaS (piattaforma), SaaS (software) — la responsabilità gestionale si sposta progressivamente verso il provider.
- **Modelli di deployment**: Public, Private, Hybrid, Community Cloud.
- **Shared responsibility**: dati e identità restano sempre responsabilità del cliente; infrastruttura fisica e rete sono del provider; livelli intermedi variano per tipo di servizio.
- **Costi pubblici cloud**: pay-per-use, ma attenzione ai costi nascosti (egress banda, IP pubblici, auto-scaling illimitato, storage).
- **Trend**: il mercato si muove verso cloud-first e multi-cloud; l'hybrid cloud (71% delle aziende) è oggi la strategia prevalente.
- **Cloud Native Application**: applicazioni progettate ab-initio per il cloud — microservizi, container, CI/CD, DevOps.

---

## Il pendolo dell'IT

L'IT oscilla ciclicamente tra centralizzazione e distribuzione:

| Epoca | Modello | Note |
|-------|---------|------|
| Anni '60 | Mainframe | Centralizzato |
| Anni '80 | Client-server | Distribuito |
| Anni 2000 | Cloud | Ricentralizzazione |
| Anni 2020 | Edge | Distribuzione al confine |

Il cloud computing si è affermato a partire dal 2006 con il lancio di **Amazon EC2**.

---

## Definizione NIST

Le **5 caratteristiche essenziali** (NIST SP 800-145):

| Caratteristica | Descrizione |
|----------------|-------------|
| **On-demand self-service** | Il consumer provisiona risorse autonomamente, senza interazione umana con il provider |
| **Broad network access** | Risorse accessibili via rete standard da qualsiasi dispositivo (mobile, laptop, workstation) |
| **Resource pooling** | Risorse fisiche/virtuali condivise tra più consumer (multi-tenant); posizione fisica astratta |
| **Rapid elasticity** | Capacità provisionabile/rilasciata elasticamente, anche automaticamente; appare illimitata al consumer |
| **Measured service** | Consumo misurato e ottimizzato automaticamente; base per il modello pay-per-use |

Requisiti alternativi (acronimo **CLOUD**): **C**ommon, **L**ocation-independent, **O**nline **U**tility on **D**emand.

---

## Modelli di delivery (XaaS)

### Tassonomia principale

| Modello | Gestito dal provider | Gestito dal cliente | Esempi |
|---------|---------------------|---------------------|--------|
| **On-premises** (IT tradizionale) | Niente | Tutto | Datacenter aziendale |
| **IaaS** | Networking, Storage, Servers, Virtualization | OS, Middleware, Runtime, App, Data | AWS EC2, Azure VM, Google Compute Engine |
| **PaaS** | + OS, Middleware, Runtime | App, Data | Google App Engine, Azure App Service, AWS Elastic Beanstalk |
| **SaaS** | Tutto | Dati e dispositivi client | Microsoft 365, Google Workspace, Salesforce |

### Servizi specializzati (XaaS)

| Acronimo | Servizio | Esempi per cloud |
|----------|---------|-----------------|
| **CaaS** | Container as a Service | Google Kubernetes Engine, AKS, Amazon ECS |
| **FaaS** (Serverless) | Function as a Service | AWS Lambda, Azure Functions, Google Cloud Functions |
| **DBaaS** | Database as a Service | Cloud SQL, Azure DB, AWS RDS |
| **KaaS** | Kubernetes as a Service | GKE, AKS, EKS |
| **BaaS** | Backup as a Service | — |
| **DRaaS** | Disaster Recovery as a Service | — |
| **Object Storage** | Storage a oggetti | S3, Azure Blob, GCP Blob Storage |

---

## Modelli di deployment

| Tipo | Descrizione | Pro | Contro |
|------|-------------|-----|--------|
| **Public Cloud** | Infrastruttura condivisa, gestita da provider terzo (AWS, Azure, GCP) | Elasticità infinita, no CapEx | Meno controllo, costi egress, lock-in |
| **Private Cloud** | Infrastruttura dedicata on-premises (es. OpenStack, VMware SDDC) | Controllo totale, compliance | Costi fissi, scalabilità limitata |
| **Hybrid Cloud** | Integrazione orchestrata di private + public cloud | Flessibilità, burst verso il pubblico | Complessità di integrazione |
| **Community Cloud** | Condiviso tra organizzazioni con requisiti simili (es. PA) | Costi ridotti rispetto al privato | Meno flessibile del pubblico |
| **Multi-cloud** | Uso di più provider pubblici (e/o privati) per servizi diversi | Evita lock-in, best-of-breed | Complessità operativa, governance |

**Hybrid vs Multi-cloud**: nell'hybrid le componenti *lavorano insieme* (dati e processi si intrecciano); nel multi-cloud ogni cloud rimane nel proprio silo, usato per task distinti.

### Soluzioni private/hybrid cloud

**Proprietarie**: VMware SDDC (Cloud Foundation + vRealize), Microsoft Azure Stack / Azure Arc, AWS Outposts, GCP Anthos.

**Open source**: OpenStack, OpenNebula.

---

## Provider pubblici principali

Quote di mercato (Q3 2022, Synergy Research Group — $217B totali):

| Provider | Quota |
|---------|-------|
| AWS | 34% |
| Microsoft Azure | 21% |
| Google Cloud | 11% |
| Alibaba Cloud | 5% |
| IBM Cloud | 3% |
| Salesforce | 3% |

In Italia sono presenti: AWS Region, Azure Region, Cloud.it, Noovle+Google.

---

## Infrastruttura geografica del cloud

### Regions e Availability Zones (AZ)

- **Region**: area geografica che raggruppa più datacenter (es. "West Europe").
- **Availability Zone**: location fisica distinta all'interno di una region, con alimentazione, raffreddamento e rete indipendenti. Ogni region ha ≥3 AZ.
- Le AZ proteggono da single-point-of-failure a livello datacenter e aumentano la disponibilità (SLA più alto).

### Contenitori logici per provider

| Provider | Concetto | Descrizione |
|---------|---------|-------------|
| AWS | Virtual Private Cloud (VPC) | Rete virtuale isolata |
| Azure | Resource Group + vNET | Contenitore logico risorse + rete virtuale |
| GCP | Project + VPC | Unità organizzativa + rete virtuale |

---

## Caratteristiche di qualità del cloud

| Caratteristica | Note |
|---------------|------|
| **Affidabilità / Disponibilità** | SLA dichiarati nei contratti; Cloud Design Patterns per HA |
| **Scalabilità** | IaaS/PaaS: scale-in e scale-out; SaaS: gestita dal provider |
| **Performance** | Latenza dipende dalla region; servizi edge riducono la distanza |
| **Manageability** | Portal web, CLI, API, IaC (Infrastructure as Code); automazione tramite cloud shell |

### Auto-scaling

Funzionalità di IaaS/PaaS che gestisce automaticamente il numero di istanze in base al carico:
- **Scale-out**: aggiunge istanze quando la CPU supera la soglia superiore.
- **Scale-in**: rimuove istanze quando il carico scende sotto la soglia inferiore.
- **Max istanze**: limite configurabile per controllare i costi.
- Implementato come un cluster load-balanced gestito.

---

## Quando conviene il cloud pubblico?

Quattro pattern di carico in cui il public cloud è conveniente:

| Pattern | Descrizione | Motivo cloud |
|---------|-------------|-------------|
| **On & Off** | Attività batch con periodi di inattività | Paghi solo quando attivo |
| **Crescita rapida** | Servizi in rapida espansione | Hardware fisico non arriva in tempo |
| **Carichi improvvisi** | Picchi imprevedibili | Elasticità immediata |
| **Carichi ciclici** | Picchi periodici (es. stagionali) | Evita sovracapacità costante |

---

## Costi del public cloud

**Modelli di pricing**:
- **On-demand**: paghi per ora/secondo usato.
- **Reserved**: sconto significativo (fino al 70%) in cambio di impegno 1-3 anni.
- **Spot/Preemptible**: istanze a basso costo soggette a interruzione.

**Costi "nascosti" da considerare**:
- **Egress**: trasferimento dati in uscita dal cloud (i big provider addebitano; OVH e Scaleway no).
- IP pubblici statici.
- Storage persistente.
- Auto-scaling illimitato.
- Bandwidth tra regioni diverse.

---

## Shared Responsibility Model

Il modello di responsabilità condivisa definisce chi gestisce cosa:

| Livello | SaaS | PaaS | IaaS | On-prem |
|---------|------|------|------|---------|
| Dati e identità | Cliente | Cliente | Cliente | Cliente |
| Applicazioni | Provider | Cliente | Cliente | Cliente |
| OS | Provider | Provider | Cliente | Cliente |
| Virtualizzazione | Provider | Provider | Provider | Cliente |
| Infrastruttura fisica | Provider | Provider | Provider | Cliente |

**Sempre responsabilità del cliente**: dati, dispositivi, account e identità.

---

## Sicurezza nel cloud

### Rischi principali percepiti

Sicurezza (74,6%), difficoltà di ritorno all'on-prem (45%), performance (18%), carenza fornitori (15%), integrazione con sistemi esistenti (13%).

### Minacce specifiche

- **Ransomware-as-a-Service**: ecosistema criminale industrializzato; doppia/tripla estorsione dal 2020.
- **Shadow IT**: dipendenti usano servizi cloud non autorizzati → rischi sicurezza e compliance.
- **Credential compromise**: attacchi a identità cloud (IAM misconfiguration).

### Best practice architetturali (AWS Well-Architected Framework — 6 pilastri)

1. **Operational Excellence** — automazione, monitoring, miglioramento continuo.
2. **Security** — sicurezza a ogni layer; "build security in every layer".
3. **Reliability** — design for failure; ridondanza multi-AZ.
4. **Performance Efficiency** — scelta del servizio giusto per il workload.
5. **Cost Optimization** — rightsizing, reserved instances, waste elimination.
6. **Sustainability** — riduzione impatto ambientale.

**Principi aggiuntivi AWS**: think parallel, loose coupling, don't fear constraints, implement elasticity.

---

## Cloud Native Application (CNA)

Un'applicazione cloud-native è progettata per sfruttare le caratteristiche uniche del cloud, non solo per girarci sopra.

**Requisiti**: scale on demand, self-heal, zero-downtime rolling updates, run anywhere.

**Pilastri tecnici**:

| Pilastro | Descrizione |
|---------|-------------|
| **Microservizi** | Componenti indipendenti comunicanti via API; deployment e scaling separati |
| **Container** | Packaging ottimale dei microservizi e delle dipendenze |
| **CI/CD** | Always be shipping; automazione build-test-deploy |
| **DevOps** | Goals condivisi tra dev e ops; infrastructure as code |

**12-Factor App** (12factor.net): metodologia per applicazioni moderne — codebase in VCS, dipendenze esplicite, configurazione in env, backing services, build/release/run separati, processi stateless, ecc.

### Pets vs. Cattle (e oltre)

| Analogia | Corrisponde a | Filosofia |
|---------|--------------|-----------|
| **Pets** | Server fisici, VM tradizionali | Nominati, curati individualmente, irremplazabili |
| **Cattle** | Istanze cloud | Fungibili, sostituiti automaticamente se falliscono |
| **Chickens** | Container | Ancora più effimeri, alta densità |
| **Insects** | Funzioni serverless | Ultra-effimeri, eseguiti a evento |

---

## Migration strategy: Lift & Shift vs. Cloud Native

| Strategia | Descrizione | Pro | Contro |
|-----------|-------------|-----|--------|
| **Lift & Shift** | Sposta l'applicazione as-is sul cloud | Veloce, rischio minimo | Non sfrutta i vantaggi del cloud |
| **Re-platform** | Piccoli adattamenti durante la migrazione | Bilanciato | Parziale ottimizzazione |
| **Cloud Native** | Riprogetta l'applicazione per il cloud | Massimo beneficio | Costo e tempo elevati |

Attenzione: a volte si migra verso il *modello cloud* (private cloud on-premises) e non verso un cloud pubblico.

---

## Evoluzione: Edge e Fog Computing

- **Edge Computing**: elaborazione dei dati vicino a dove vengono generati, riducendo latenza e consumo di banda. Evoluzione delle CDN; usato in IoT e real-time analytics.
- **Fog Computing**: sinonimo di Edge Computing (termine promosso da OpenFog Consortium, fondato nel 2015 da Cisco, Intel, Microsoft, Dell, ARM; fuso con Industrial Internet Consortium nel 2019).

**Cloud vs Edge**:

```
Cloud: Devices → (Internet) → Cloud (elaborazione centrale)
Edge:  Devices → Edge platform → Cloud (elaborazione locale + centrale)
```

---

## Cloud Management Platform

Strumento per gestire risorse cloud (provisioning, monitoring, costi, governance):

| Scenario | Soluzione tipica |
|---------|-----------------|
| Public cloud | Portal nativo (Azure Portal, AWS Console) |
| Private cloud | OpenStack, VMware vRealize Automation |
| Hybrid/Multi-cloud | Microsoft Azure Arc, Google Anthos, AWS Outposts |

Funzionalità richieste: resource manager, orchestrator, monitor, cost analyzer, IAM integration.

**Azure Resource Manager** esempio: layer di gestione unificato che permette deploy, update, delete delle risorse Azure tramite portal, PowerShell, CLI, REST API o SDK; supporta ARM templates (IaC dichiarativo).

---

## Normative e compliance

| Normativa | Ambito | Impatto cloud |
|-----------|--------|--------------|
| **GDPR** (EU) | Privacy e trattamento dati personali | Impone dove possono risiedere i dati dei cittadini EU |
| **Data Cloud Act** (USA) | Accesso ai dati da autorità USA | Rischio per dati EU su provider americani |
| **Garante Privacy IT** | Linee guida uso consapevole del cloud | Indicazioni per PA italiana |
| **IEEE P2301** | Cloud Portability and Interoperability Profiles | Standard interoperabilità |
| **IEEE P2302** | Intercloud Interoperability and Federation | Standard federazione cloud |

---

## Articoli correlati

- [[servizi-infrastrutturali]]
- [[virtualizzazione]]
- [[server-windows]]
- [[server-linux]]
- [[figura-amministratore-di-sistema]]

## Fonti

- `raw/4 - CloudComputing_COMPILED.pdf` — Slide del corso Amministrazione di Sistema, UniBS, a.a. 2024-25
