---
tags: [virtualizzazione, hypervisor, vm, container, docker, kubernetes, vdi, amministrazione-di-sistema]
data_creazione: 2026-05-28
data_aggiornamento: 2026-05-28
fonti:
  - raw/5 - Virtualizzazione.pdf
---

# Virtualizzazione

La **virtualizzazione** è un livello di astrazione che permette di creare contesti multipli partizionando le risorse hardware e software di un elaboratore, rendendole disponibili al software in forma di risorsa virtuale. Qualunque risorsa può essere virtualizzata: CPU, RAM, disco, rete, SO, applicazioni. Nella pratica enterprise si distinguono tre grandi aree: **virtualizzazione server** (VM), **container** e **VDI** (desktop virtuale).

## Punti chiave

- **Hypervisor Type-1**: gira direttamente sull'hardware (bare-metal); overhead <5%; esempi: VMware ESXi, Hyper-V, KVM/Xen.
- **Hypervisor Type-2**: gira sopra un OS host; esempi: VirtualBox, VMware Workstation.
- **Container**: virtualizzazione a livello OS (namespaces + cgroups); condividono il kernel host; avvio in millisecondi; Docker è lo standard de facto.
- **Kubernetes (K8s)**: orchestratore di container su cluster multi-host; standard de facto per ambienti cloud-native.
- **VM vs Container**: VM = isolamento forte + OS guest separato; Container = efficienza massima + isolamento parziale (kernel condiviso).
- **VDI**: desktop (Windows) in esecuzione su VM centralizzate in datacenter; accesso remoto tramite thin client.
- **ROI virtualizzazione**: ritorno dell'investimento in <6 mesi; riduzione TCO fino a 64%; downtime ridotto del 25-55%.

---

## Storia

| Epoca | Evento |
|-------|--------|
| Anni '60 | IBM VM/370 introduce il concetto di macchina virtuale (SuperVisor) |
| Anni '90 | JVM (Java Virtual Machine) riaccende interesse per la portabilità |
| 1999 | Primi prodotti di virtualizzazione x86 (VMware) |
| 2003 | VMware introduce la live migration (vMotion) |
| Anni 2000 | Data Center 3.0: i server virtuali superano i fisici |
| 2013 | Docker lancia i container moderni |
| 2014 | Kubernetes (Google) diventa orchestratore standard |
| 2016 | 75% dei workload x86 in VM (Gartner) |

**Evoluzione datacenter**: 1.0 Mainframe → 2.0 Low-End Server → 3.0 Virtual Server.

---

## Categorie di virtualizzazione

| Categoria | Descrizione | Esempi |
|-----------|-------------|--------|
| **System / Server** | VM che emula un server completo | VMware ESXi, Hyper-V, KVM |
| **Desktop** | VM con OS client (VDI o locale) | VMware Horizon, Citrix XenDesktop |
| **Application** | App isolata senza VM completa | App-V, ThinApp |
| **Presentation** | Solo la schermata remota | Microsoft RDS, Citrix XenApp |
| **User State** | Profilo utente separato dall'OS | Folder Redirection, Roaming Profiles |
| **Storage** | Astrazione delle risorse disco | SAN, SDS, LUN virtualization |
| **LAN/Network** | Astrazione della rete | VLAN, VPN, SDN, NFV, SD-WAN |
| **Data** | Astrazione del layer dati | Data virtualization layer |

---

## Tipi di virtualizzazione hardware

### Partizionamento hardware (Type-0)
Partizioni hardware native su sistemi enterprise: IBM LPAR/DLPAR/SPLPAR, HP nPar/vPar, Oracle LDOMs/Zones, Intel Hyper-Threading/VT.

### Virtualizzazione completa (Full Virtualization)
Il guest OS gira non modificato. Il VMM intercetta le istruzioni privilegiate con:
- **Trap and Emulate**: le istruzioni sensitive fanno trap al VMM.
- **Binary Translation**: riscrittura dinamica delle istruzioni sensitive (VMware ESX storico).
- **Hardware-Assisted**: CPU estende il set di anelli con Ring -1 per il VMM (Intel VT-x, VT-i, VT-d, VT-c; AMD-V).

### Paravirtualizzazione
Il guest OS è modificato per chiamare direttamente il VMM tramite **hypercall** invece di usare istruzioni privilegiate. Overhead minore; driver I/O più efficienti (virtio). Usata da Xen (architettura Domain0 / DomainU).

### OS-level virtualization (Container)
Nessuna virtualizzazione dell'hardware: i processi condividono il kernel host, isolati tramite **namespaces** (PID, rete, filesystem, IPC) e **cgroups** (limitazione risorse). Overhead quasi zero.

### Emulazione
Traduzione di istruzioni da un'architettura a un'altra (es. QEMU emula ARM su x86). Più lento della virtualizzazione; utile per portabilità cross-arch.

---

## Modelli di hypervisor

| Tipo | Posizione | Caratteristiche | Esempi |
|------|-----------|----------------|--------|
| **Type-0** | Hardware | Partizioni HW native | IBM LPAR, HP nPar |
| **Type-1 Monolithic** | Bare-metal | Driver nell'hypervisor; superficie d'attacco maggiore | VMware ESX/ESXi (VMkernel 32 MB) |
| **Type-1 Microkernel** | Bare-metal | Driver nei guest; hypervisor minimo (~600 KB); più sicuro | Microsoft Hyper-V, Xen |
| **Type-2 Hosted** | Sopra Host OS | Semplice installazione; overhead doppio | VirtualBox, VMware Workstation |

**Microsoft Hyper-V** usa un design microkernel: Windows (core) gira nella partizione parent con VSP (Virtual Service Providers); le VM figlie hanno VSC (Virtual Service Clients) che comunicano tramite VMBus. L'hypervisor gira a Ring -1.

**VMware ESX/ESXi**: VMkernel (32 MB) gestisce direttamente lo storage, la rete e le risorse. La Service Console RHEL3 (2 GB) è stata eliminata in ESXi.

### Intel Virtualization Technology (Intel VT)

| Componente | Standard | Funzione |
|-----------|---------|---------|
| **VT-x / VT-x2** | CPU (Xeon) | Supporto hardware per VM x86/x64; Extended Page Tables |
| **VT-i** | CPU (Itanium) | Come VT-x per IA-64 |
| **VT-d / VT-d2** | Chipset | I/O performance tramite direct assignment; memory protection |
| **VT-c** | Network/I/O | >2x throughput 10GbE; offload I/O |

---

## Virtualizzazione delle risorse VM

| Risorsa | Metodo | Note |
|---------|--------|------|
| **CPU** | Full virtualization | 1 vCPU = 1 pCore; over-provisioning possibile (rapporto <4:1) |
| **RAM** | Full virtualization | Memory balloon, TPS (Transparent Page Sharing), NUMA |
| **Disco** | Emulazione / Paravirtual | 1 disco virtuale = 1 file (VMDK, VHD, qcow2) |
| **Rete** | Emulazione / Paravirtual | Virtual switch (vSwitch) + vNIC; VLAN, port group |
| **GPU** | Pass-through / vGPU | SR-IOV, NVIDIA vGPU, Intel GVT |

---

## Prodotti enterprise

### VMware vSphere
Stack enterprise di riferimento (84% market share storico):
- **ESXi**: hypervisor Type-1 monolitico.
- **vCenter Server**: gestione centralizzata del cluster (Management/Control Plane).
- **vCLS**: cluster di VM di sistema per mantenere il control plane.
- **DRS** (Distributed Resource Scheduler): bilanciamento automatico del carico tra host.
- **HA**: riavvio automatico delle VM su altro host in caso di guasto.
- **vMotion**: live migration (introdotta 2003) — migra RAM + stato CPU senza interruzione.
- **Storage vMotion**: migra il disco della VM tra datastores.

### Microsoft Hyper-V
Hypervisor microkernel integrato in Windows Server; "Viridian":
- Gestione: System Center Virtual Machine Manager (SCVMM).
- Vantaggio: licenza inclusa in Windows Server, supporto Windows ottimale.

### KVM / Xen (Linux)
- **KVM** (Kernel-based VM): modulo Linux; hypervisor Type-1 ibrido; interfaccia QEMU per emulazione periferiche.
- **XEN**: Type-1 microkernel; Domain0 gestisce I/O; paravirtualizzazione nativa.
- **Proxmox VE**: distro basata su KVM + LXC; open source.

---

## VM migration

| Tipo | Descrizione |
|------|-------------|
| **Cold migration** | VM spenta, spostata su altro host o datastore |
| **Live migration (vMotion)** | VM in esecuzione, stato RAM copiato iterativamente; finestra di stop ~ms |
| **Cross-datastore migration** | Sposta i file disco della VM |
| **Cross-cloud migration** | Problema del formato VM (VMDK vs VHD vs qcow2) e del formato disco |

**Sicurezza in virtualizzazione**:
- Proteggere l'hypervisor dalle VM (VM escape via bug hypervisor/HW).
- Proteggere le VM dall'hypervisor (VM Trusted Platform).
- Proteggere le comunicazioni di live migration e storage condiviso.
- **TPM / vTPM**: misura l'integrità dell'hypervisor e delle VM.
- **Intel SGX**: enclavi protette nella CPU.
- **Intel TDX** (Trust Domain Extensions): VM con memoria privata cifrata e chiave TD privata — protegge anche dall'hypervisor.

---

## Prestazioni e criticità

| Problema | Descrizione |
|---------|-------------|
| **Hypervisor Tax** | Overhead dell'hypervisor (<5%) |
| **Noisy Neighbors** | VM sullo stesso host che consumano risorse in modo irregolare |
| **I/O Blender Effect** | I/O multipli da VM diverse si mescolano → IOPS piccoli, frammentati, random |
| **VM Sprawl** | Proliferazione incontrollata di VM non gestite |

---

## Container

I **container** sono oggetti logici portabili con packaging standard (Code + Dependencies + Configuration + Runtime). Operano a livello OS, non virtualizzano l'hardware.

### Container vs. VM

| Dimensione | VM | Container |
|-----------|----|---------:|
| Isolamento | Forte (OS separato) | Parziale (kernel condiviso) |
| Overhead | Moderato (hypervisor tax) | Quasi zero |
| Boot time | Minuti | Millisecondi |
| Dimensione | GB | MB |
| Portabilità | Limitata (formato proprietario) | Elevata (OCI standard) |
| Sicurezza | Più robusta | Dipende dalla configurazione |
| Caso d'uso | Workload stateful, OS legacy | Microservizi, cloud-native apps |

### Tecnologie Linux per i container

- **Linux namespaces**: isolamento di PID, rete, filesystem, IPC, UTS, user.
- **cgroups** (control groups): limitazione e contabilità delle risorse (CPU, RAM, I/O).
- **LXC** (Linux Containers): dal kernel 2.6.24.
- **Predecessori**: FreeBSD jail, Solaris Zones/Containers, OpenVZ, Linux-VServer.
- **Windows Containers**: da Windows Server 2016.

### Docker

**Docker Engine**: runtime container de facto standard.
- **Docker Daemon** (`dockerd`): gestisce container, immagini, reti, volumi; espone REST API.
- **Docker CLI**: interagisce con l'API.
- **Containerd**: runtime di basso livello (anche runtime standalone).
- **runC**: implementazione di riferimento OCI per eseguire container.

**Terminologia**:
- **Image**: filesystem read-only stratificato (layers unionFS).
- **Container**: istanza in esecuzione di un'image.
- **Registry**: repository di immagini. Docker Hub = marketplace default (Docker Official Images, Verified Publishers). Self-hosted: Artifactory, Harbor, Quay.

**Comandi base**:
```bash
docker run --name mongo -d mongo            # container background
docker run --rm -it ubuntu                  # interattivo, auto-remove
docker run -d -p 80:80 nginx               # port mapping host:container
docker run -v mysql-data:/var/lib/mysql ... # volume persistente
```

**Risorse di un container**: usa le risorse dell'host; disco in overlay filesystem; rete in virtual switch su rete privata (ogni container ha IP proprio; port mapping per esposizione esterna).

### Microservizi

Architettura che struttura l'applicazione come insieme di servizi:
- Altamente manutenibili e testabili.
- Loosely coupled.
- Indipendentemente deployabili.
- Organizzati per business capability.

---

## Kubernetes (K8s)

**Kubernetes** è il sistema open-source di orchestrazione di container su cluster multi-host. Standard de facto per cloud-native; sviluppato da Google, ora CNCF.

### Componenti principali

| Componente | Ruolo |
|-----------|-------|
| **kubectl** | CLI per amministrare il cluster |
| **Master Node / Control Plane** | Gestisce il cluster; API server (K8s API); scheduler; controller manager; etcd |
| **Worker Node** | Esegue i workload; ospita kubelet + container engine |
| **Kubelet** | Agent sul nodo; assicura che i container siano running |
| **Pod** | Unità minima di scheduling; 1+ container + storage volumes; IP univoco |
| **Deployment** | Definisce lo stato desiderato; gestisce repliche dei Pod |

### Funzionalità Kubernetes

- **Service discovery e load balancing**: via DNS o IP; distribuzione automatica del traffico.
- **Orchestrazione storage**: monta automaticamente storage locale o cloud (CSI).
- **Rollout / Rollback**: deploy graduale; rollback automatico in caso di errore.
- **Ottimizzazione carichi**: bin-packing automatico dei Pod sui nodi.
- **Self-healing**: riavvio container, sostituzione Pod falliti, health check.
- **Secrets e ConfigMap**: gestione di credenziali e configurazione senza rebuild dell'immagine.
- **Horizontal Pod Autoscaling**: scala il numero di Pod in base a CPU/metriche custom.

### Modello dichiarativo

Kubernetes usa un modello **dichiarativo** (non imperativo): si dichiara lo *stato desiderato* (YAML/JSON) e il sistema riconcilia continuamente verso quello stato (self-healing).

### Networking e Storage nei container

**CNI (Container Network Interface)**: standard per plugin di rete. Built-in: loopback, bridge, ipvlan, dhcp, flannel. Third-party: Calico, Cilium, Weave.

**CSI (Container Storage Interface)**: standard per collegare storage backend a container; driver per ogni storage (NFS, Ceph, AWS EBS, Azure Disk, ecc.).

**Pod network**: ogni Pod ha IP univoco; container nello stesso Pod condividono namespace di rete e volumi.

### Kubernetes as a Service (KaaS)

Disponibile gestito su tutti i cloud: AKS (Azure), EKS (AWS), GKE (Google).

---

## VDI (Virtual Desktop Infrastructure)

**VDI**: desktop (OS client Windows) in esecuzione come VM su hypervisor in datacenter. L'utente accede tramite thin client o browser via protocollo remoto (RDP, ICA/HDX, PCoIP).

### Componenti VDI

- **Hypervisor**: host delle VM desktop.
- **Connection Broker**: instrada le richieste utente alla VM corretta.
- **Storage**: DAS/SAN/VDI-optimized (importante per boot storm).
- **Management**: image management, policy, provisioning.

### Tipi di VDI

| Tipo | Descrizione |
|------|-------------|
| **Persistent (Private)** | Desktop dedicato a un utente; modifiche salvate |
| **Non-Persistent (Pooled)** | Desktop assegnato dinamicamente; reset al logout |
| **Layered / Hybrid** | Base image condivisa + layer personale |
| **RDSH (Shared)** | Terminal Server: più utenti sullo stesso OS server |

### Percorso da PC a VDI

```
PC                    → costi HW/SW, no gestione centrale
Terminal Server       → gestione centrale, isolamento parziale, limiti applicativi
VDI                   → gestione centrale + qualunque applicazione + isolamento completo
```

### Soluzioni VDI

**On-premises**: Citrix XenDesktop/CVAD, VMware Horizon, Microsoft RDS.

**Cloud / DaaS**:
- **Azure Virtual Desktop** (AVD): PaaS; management plane cloud; host su Azure IaaS.
- **Windows 365 (Cloud PC)**: SaaS; costo fisso per utente; no skills VDI richiesti.
- **Amazon Workspace**: DaaS su AWS.
- **Nutanix Frame**: management plane cloud + HCI on-prem o IaaS.

**CCON (Client Consolidation)**: continuum da Terminal Server (sessioni condivise) a Client Virtualization (VM dedicate) a Workstation Blade (HW dedicato remoto).

---

## Serverless

Evoluzione beyond container: il runtime serverless esegue **funzioni** (singoli handler stateless) senza gestire server o container. Esempi: AWS Lambda, Azure Functions, Google Cloud Functions.

Stack confronto:
```
VM:         App + Guest OS + Hardware virtuale + Hypervisor + Host OS + HW fisico
Container:  App + Dipendenze + Motore contenitore + Host OS + HW fisico
Serverless: Funzione + Runtime serverless + Host OS + HW fisico
```

---

## Articoli correlati

- [[cloud-computing]]
- [[server]]
- [[hardware-cpu-server]]
- [[storage-enterprise]]
- [[high-availability-e-cluster]]
- [[sistemi-enterprise-e-integrazione]]

## Fonti

- `raw/5 - Virtualizzazione.pdf` — Slide del corso Amministrazione di Sistema, UniBS, a.a. 2024-25
