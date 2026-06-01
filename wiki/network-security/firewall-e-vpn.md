---
tags: [network-security, firewall, VPN, DMZ, nftables, netfilter, IPSec, NAT, sicurezza-perimetrale]
data_creazione: 2026-05-31
data_aggiornamento: 2026-05-31
fonti:
  - raw/2026-netsec-2f-firewall_COMPILED.pdf
---

# Firewall e VPN

Un firewall è il componente principale del perimetro di sicurezza di una rete: separa l'interno dall'esterno, filtra il traffico e può includere VPN per far diventare outsider temporaneamente insiders. Su Linux, il subsistema **Netfilter** e il tool **nftables** implementano queste funzionalità.

## Punti chiave

- Il **perimetro di sicurezza** definisce chi è insider e chi è outsider; non è sempre facile da delimitare.
- I firewall applicano politiche **stateless** (basate su header) o **stateful** (basate sullo stato della connessione).
- La **DMZ** (De-Militarized Zone) ospita i server accessibili sia dall'interno che dall'esterno.
- Le **VPN layer-3** (IPSec) rendono un outsider insider a livello IP; le **VPN layer-4** (SSL/TLS, SSH) per classi specifiche di traffico.
- Su Linux, **Netfilter** agisce tramite 5 hook nel kernel (PREROUTING, INPUT, FORWARD, OUTPUT, POSTROUTING); **nftables** configura le regole via userspace.
- Le regole **stateful** (`ct state established,related`) sono essenziali per i protocolli bidirezionali.

## Il perimetro di sicurezza

Un perimetro di sicurezza divide una rete in zone con diversi livelli di fiducia. Un firewall sta al confine e controlla l'accesso.

**Funzioni principali:**
- Access control: decidere quali flussi di traffico sono permessi.
- Monitoring: registrare eventi sospetti.
- Differenziare tipi di outsider (es. Internet generica vs. partner commerciali).

**Il problema "chi protegge chi":**
- In un'università, lo stesso firewall deve proteggere i server (dall'esterno) ma anche proteggere l'esterno dagli utenti interni (malicious or naive users, unmanaged hosts).
- Distinguere "protecting ME from the world" e "protecting the world from ME".

## Architetture di rete con firewall

### DMZ — De-Militarized Zone

Una DMZ è una subnet connessa all'interfaccia del firewall con la security policy più bassa. Ospita server che devono essere raggiungibili sia dall'interno che dall'esterno (es. email, DNS, VPN servers).

**Architettura con un firewall:**
```
Internet ──→ Firewall ──→ DMZ (server pubblici)
                    └──→ LAN interna (NAT)
```

**Architettura con due firewall:**
```
Internet ──→ Firewall 1 ──→ DMZ
                        └──→ Firewall 2 ──→ LAN interna
```

Con DMZ e multi-gateway, il firewall è sempre anche un router. Le regole non sono gerarchiche ma una lista ordinata da parsare → latenza crescente con la complessità (L3 < L4 < L7 < L9).

### Posizionamento del firewall

**Sul proprio host**: massimo controllo, nessun problema di privacy, ma può diventare un collo di bottiglia (DoS triviale).

**Il più lontano possibile dall'host**: blocca i pacchetti malevoli alla sorgente ("slam it in the face of the attacker"); si integra spesso con il router. Le funzioni più complesse possono essere delegate al cloud.

**Ridondanza**: i firewall sono Single Point of Failure (SPoF). Essendo software complesso soggetto a bug e errori di configurazione, la ridondanza è fondamentale.

## Tipi di firewall: cosa si filtra

| Livello | Criteri di filtraggio | Caratteristiche |
|---|---|---|
| **L3** | Indirizzi IP | Semplice, eseguibile in HW |
| **L4** | IP + protocollo + porte | Semplice, eseguibile in HW |
| **L7** | Contenuto dei pacchetti | Complesso, stateful, solo SW |
| **L9 / AI** | Significato/semantica del flusso | Molto complesso, AI e reti neurali |

### Configurazione del firewall

- **Evitare regole generiche**: es. la porta 25 (SMTP) deve essere aperta solo verso i mail server, non verso tutti i server in DMZ.
- **Non filtrare completamente ICMP**: bloccare selettivamente (redirect, timestamp-request sono pericolosi; echo-request va limitato, non bloccato).
- **Protocolli che violano il layering** (FTP, H.323, Chat) richiedono Application Level Gateway (ALG).
- **ALG / Proxy**: intermediari che terminano e reiniziano le connessioni; necessari quando ci sono NAT coinvolti.

## VPN — Virtual Private Network

Una VPN crea un canale sicuro attraverso una rete non fidata (Internet), permettendo a outsider di diventare temporaneamente insider.

### VPN Layer-3

Aprono un canale virtuale a livello IP: il client ottiene un indirizzo interno e viene trattato come insider a tutti gli effetti.

| Tecnologia | Note |
|---|---|
| **IPSec** | Standard; usato nella maggior parte delle VPN aziendali |
| **PPTP/L2TP** | Layer-2 tunneling; meno sicuro, legacy |

### VPN Layer-4

Il tunnel avviene al livello di trasporto: solo classi specifiche di traffico vengono instradate nella VPN.

| Tecnologia | Note |
|---|---|
| **SSL/TLS (stunnel)** | Tunnel HTTPS; può avere problemi con connessioni TCP-over-TCP a lungo termine |
| **SSH** | Tunneling SSH; comodo per accesso remoto |

## Aspetti legali

Nei paesi occidentali, leggere comunicazioni private senza consenso è un reato. I firewall (inclusi quelli anti-spam) "leggono" il contenuto → **tecnicamente illegali**, ma necessari.

**Soluzione pratica:**
- Processamento automatizzato (nessun essere umano coinvolto).
- **Principio di proporzionalità**: non spingere l'analisi del traffico oltre ciò che è necessario per la sicurezza.
- Informare gli utenti (raramente fatto in pratica).
- Usare solo regole "don't": "tutto ciò che non è vietato è permesso". In pratica si fa spesso l'opposto.

## Linux Firewalls: Netfilter e nftables

### Netfilter

**Netfilter** è il sottosistema del kernel Linux specializzato nel firewalling. Opera tramite **hook** (punti di controllo nel codice di rete):

```
NIC ──→ PREROUTING ──→ INPUT ──→ Local Process
                  └──→ FORWARD ──────────────────→ POSTROUTING ──→ NIC
                                                      ↑
                                               OUTPUT (Local Process)
```

| Hook | Utilizzo tipico |
|---|---|
| **INPUT** | Pacchetti destinati all'host locale (server firewall, port filtering) |
| **FORWARD** | Pacchetti che attraversano l'host come router (protezione LAN interna) |
| **OUTPUT** | Pacchetti generati localmente (limitare traffico uscente, malware containment) |
| **PREROUTING** | Prima del routing (DNAT, packet mangling) |
| **POSTROUTING** | Appena prima della NIC (SNAT, masquerading) |

### nftables

**nftables** è il tool userspace moderno per configurare Netfilter (sostituisce iptables).

**Struttura gerarchica:**
- **Table**: namespace che raggruppa chain correlate (famiglia: `inet`, `ip`, `ip6`, `arp`, `bridge`, `netdev`).
- **Chain**: lista ordinata di regole, collegata a un hook o chiamata da un'altra chain.
- **Rule**: condizione (expression) + azione (verdict).
- **Verdict**: `accept`, `drop`, `reject`, `jump`, `return`, `log`.

**Famiglie di tabelle principali:**
- `inet`: IPv4 e IPv6 (il più usato).
- `ip` / `ip6`: solo IPv4 / solo IPv6.
- `arp`, `bridge`, `netdev`: per casi specifici.

**Tipi di chain:**
- `filter`: filtraggio pacchetti.
- `nat`: operazioni NAT.
- `route`: decisioni di routing.

### Esempio minimale: firewall deny-by-default

```nft
table inet filter {
    chain input {
        type filter hook input priority 0; policy drop;

        iif lo accept                          # loopback sempre OK
        ct state established,related accept    # traffico di ritorno (stateful)
        tcp dport 22 accept                    # SSH
        ip protocol icmp accept                # ICMP
        reject                                 # tutto il resto (solo per didattica, ridondante con policy drop)
    }
}
```

**Comandi:**
```bash
sudo nft -f firewall.nft    # carica il ruleset
sudo nft list ruleset       # ispeziona il ruleset attivo
```

### Regole stateless e stateful

**Stateless**: non ricordano i pacchetti precedenti; utili per politiche semplici ma limitati per protocolli bidirezionali.

**Stateful (connection tracking)**: il kernel registra le sessioni attive. Ogni pacchetto viene classificato:
- `new`: primo pacchetto di una nuova sessione.
- `established`: fa parte di una sessione già accettata.
- `related`: correlato a una sessione esistente (es. FTP data).
- `invalid`: non appartiene a nessuna sessione nota.

La riga `ct state established,related accept` è **essenziale** in ogni firewall pratico: permette il traffico di ritorno per le sessioni già accettate senza dover scrivere regole inverse esplicite.

### Verdicts

| Verdict | Comportamento |
|---|---|
| `accept` | Lascia passare il pacchetto |
| `drop` | Scarta silenziosamente; rallenta i port scan (timeout lato mittente) |
| `reject` | Scarta e invia un errore; utile in laboratorio/debug |
| `log` | Registra il pacchetto (senza terminare); usare prima di drop/reject |

`drop` vs `reject` è una **decisione di policy di sicurezza**, non solo una scelta sintattica.

### Ordine delle regole

Le regole sono verificate **in ordine dall'alto al basso**. Il primo verdict terminante decide il destino del pacchetto. Le regole specifiche di allow devono precedere le regole generali di deny.

### NAT con nftables

Netfilter può anche tradurre indirizzi e porte:
- **SNAT / masquerade**: cambia l'indirizzo sorgente dei pacchetti uscenti (usato nei gateway).
- **DNAT**: redirige i pacchetti verso un indirizzo/porta interna diversa.

**Esempio masquerade (gateway NAT):**
```nft
table ip nat {
    chain postrouting {
        type nat hook postrouting priority 100;
        oif "eth0" masquerade
    }
}
```

## Articoli correlati

- [[sicurezza-lan]]
- [[tls]]
- [[protocolli-crittografici]]
- [[sicurezza-wireless]]

## Fonti

- raw/2026-netsec-2f-firewall.pdf
