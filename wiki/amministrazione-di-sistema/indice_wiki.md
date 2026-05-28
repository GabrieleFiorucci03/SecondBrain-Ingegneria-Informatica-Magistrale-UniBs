# Indice wiki: Amministrazione di Sistema

Knowledge base sul corso *Amministrazione di Sistema* (Ingegneria Informatica Magistrale, UniBS, primo anno primo semestre, a.a. 2024-25). Raccoglie i concetti su figura del sistemista, server, servizi di rete, cloud computing, virtualizzazione e sistemi enterprise, organizzati per consultazione e studio.

## Articoli

- [[figura-amministratore-di-sistema]] — Ruoli, compiti, requisiti e cultura professionale dell'amministratore di sistema, con riferimenti normativi (UNI, GDPR).
- [[server]] — Concetto di sistema server, tipi, caratteristiche (RAS), form factor, scelta e dimensionamento (scale-in/out, cluster), SO server.
- [[hardware-cpu-server]] — Architettura hardware (von Neumann, NUMA/UMA), CPU, evoluzione del calcolo (GPU/FPGA/xPU), ISA (CISC/RISC/ARM), SoC.
- [[memoria-ram-server]] — Memoria RAM, ECC, gerarchia delle memorie, NVDIMM/PMEM, Big Memory, interconnect CXL.
- [[storage-server]] — Storage server: HDD/SSD/NAND, RAID, NVMe, IOPS, storage tiering, bus dischi e form factor.
- [[bus-e-reti-server]] — Bus di sistema (PCIe/CXL), bus esterni, schede di rete (IEEE 802.3), NPAR/SR-IOV, SmartNIC.
- [[server-linux]] — GNU/Linux come piattaforma server: storia, software libero, kernel, distribuzioni, standard, tool (hub del modulo Linux).
- [[linux-installazione-boot]] — Installazione, partizionamento/LVM, scelta filesystem, boot (BIOS/bootloader), init/systemd, arresto, cloud-init.
- [[linux-gestione-sistema]] — Gestione operativa: clock/NTP, hardware/udev, pacchetti (DEB/RPM), processi (cron), servizi (systemctl).
- [[linux-utenti-autenticazione]] — Utenti (passwd/group/shadow), su/sudo, permessi, autenticazione (NIS/LDAP/PAM/NSS), SELinux.
- [[linux-filesystem-storage]] — Filesystem (ext4/XFS/FUSE/VDO), LVM, mount/fstab, resize/trim, gestione dischi, backup.
- [[linux-log-monitoraggio-rete]] — Log (syslog/journald), monitoraggio, configurazione rete, Open vSwitch, SSH, firewall, performance tools.
- [[server-windows]] — Windows Server: storia NT, versioni/lifecycle, edizioni, architettura, PowerShell, NTFS/ReFS, SMB, Active Directory, sicurezza (VBS, Defender Firewall), gestione remota.
- [[servizi-infrastrutturali]] — Concetti ITIL di servizio, classificazione workload (OLTP/OLAP/CPU/IO intensivo), dipendenze upstream/downstream, elenco servizi infrastrutturali e applicativi.
- [[ntp-e-dhcp]] — NTP (stratum, PTP, implementazioni) e DHCP (DORA handshake, scope/lease/reservation, relay, split scope, sicurezza).
- [[dns-server]] — DNS completo: struttura gerarchica, RR (SOA/NS/A/MX/CNAME/TXT), BIND, zone primarie/secondarie, sicurezza, DNSSEC, DoH, DoT, CoreDNS.
- [[web-server]] — Web server: HTTP 1.x/2/3, Apache (MPM, virtual host, SSL, mod), nginx, HTTPS/certificati, scalabilità (CDN, LB), sicurezza web (WAF, XSS).
- [[mail-server]] — Mail: architettura MUA/MTA/MDA, SMTP, Postfix, alias/forward, anti-spam (SPF/DKIM/DMARC), anti-virus (ClamAV/Amavis), POP3, IMAP, groupware, Exchange.
- [[ftp-server]] — FTP: protocollo, modalità attiva/passiva, tipi utenti, chroot, vsftpd, FTPS, alternative sicure (SFTP, SCP, WebDAV).
- [[cloud-computing]] — Cloud computing: definizione NIST, modelli IaaS/PaaS/SaaS, deployment (public/private/hybrid/multi-cloud), provider (AWS/Azure/GCP), shared responsibility, cloud native, edge computing, normative.
- [[sistemi-enterprise-e-integrazione]] — Sistemi enterprise, legacy, cluster, HCI, CAP theorem, xPU, interoperabilità piattaforme, IaC (Ansible/Terraform/Puppet), IAM, MFA, SSO, SIEM, monitoraggio centralizzato, migrazione 7R.
- [[active-directory-e-domini]] — Active Directory: architettura (Domain/OU/Forest/Tree), FSMO, GPO, NTDS/SYSVOL, Kerberos, LDAP, DFS, Samba, Azure Entra ID.
- [[storage-enterprise]] — DAS/NAS/SAN/Object Storage, RAID, FC/iSCSI/NVMeOF, tiering/caching, snapshot/replica/deduplica, HCI, SDS, disaggregated storage.
- [[high-availability-e-cluster]] — HA hardware, cluster HA/LB/HPC, quorum, split-brain, MSCS/Pacemaker, LVS, storage condiviso, stretched cluster.
- [[bcdr-e-backup]] — Business Continuity, RPO/RTO/MTD, livelli di disponibilità, policy backup (full/diff/incr), regola 3-2-1-1-0, LTO, storage immutabile, Cyber Recovery.
- [[virtualizzazione]] — Hypervisor Type-1/2, VM (VMware/Hyper-V/KVM), container (Docker), Kubernetes, VDI, microservizi, serverless.
