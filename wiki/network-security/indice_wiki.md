# Wiki: Network Security

Wiki del corso UniBS Network Security (2025/2026, Prof. Francesco Gringoli). Copre crittografia applicata, protocolli di sicurezza di rete, autenticazione, sicurezza wireless/mobile e sicurezza perimetrale.

## Articoli

### Crittografia: fondamenti

- [[introduzione-crittografia]] — Introduzione ai concetti di sicurezza: CIA triad, attacchi, servizi crittografici, modelli di attacco.
- [[crittografia-simmetrica]] — Cifrari simmetrici: stream cipher (RC4), block cipher (DES, AES), modalità operative (ECB, CBC, CTR, GCM).
- [[funzioni-hash]] — Funzioni hash crittografiche: proprietà (one-way, collision-resistance), SHA, HMAC, MDC vs MAC.
- [[crittografia-asimmetrica-fondamenti]] — Crittografia a chiave pubblica: fondamenti matematici, RSA overview, problema del logaritmo discreto.
- [[rsa]] — RSA: generazione chiavi, cifratura/firma, padding OAEP/PSS, attacchi pratici.
- [[diffie-hellman]] — Diffie-Hellman: scambio di chiavi, variante su curve ellittiche (ECDH), Perfect Forward Secrecy.

### Crittografia: protocolli e infrastruttura

- [[protocolli-crittografici]] — Protocolli crittografici: nonce, challenge-response, autenticazione mutua, EAP.
- [[pki]] — Public Key Infrastructure: certificati X.509, CA, CRL, OCSP, trust store.
- [[tls]] — TLS (Transport Layer Security): handshake, cipher suite, record protocol, TLS 1.3.

### Sicurezza nelle reti locali

- [[sicurezza-lan]] — Sicurezza nelle LAN (IEEE 802.1x, EAP, RADIUS, AAA): autenticazione porta, EAP methods, EAPOL, DIAMETER.
- [[sicurezza-wireless]] — Sicurezza nelle WLAN (WEP, WPA, WPA2, WPA3): vulnerabilità WEP, 4WHS, TKIP, CCMP, KRACK attack, SAE.

### Sicurezza nelle reti mobili

- [[sicurezza-reti-mobili]] — Sicurezza nelle reti cellulari (GSM/2G, UMTS/LTE/3-4G, 5G): AKA, Kasumi, IMSI catcher, SUCI/SUPI.

### Sicurezza perimetrale

- [[firewall-e-vpn]] — Firewall e VPN: perimetro di sicurezza, DMZ, VPN L3/L4, Netfilter/nftables su Linux, regole stateless e stateful, NAT.
