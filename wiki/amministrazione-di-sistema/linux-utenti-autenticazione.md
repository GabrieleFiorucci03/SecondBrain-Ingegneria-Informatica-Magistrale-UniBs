---
tags: [amministrazione-di-sistema, linux, utenti, autenticazione, pam, selinux, permessi]
data_creazione: 2026-05-28
data_aggiornamento: 2026-05-28
fonti:
  - raw/2 - ServerLinux_COMPILED.pdf
---

# Utenti e autenticazione in Linux

La gestione degli utenti, dei permessi e dell'autenticazione è centrale per la sicurezza di un server Linux. Si basa su file di sistema, identificatori numerici (UID/GID), framework di autenticazione modulari e meccanismi di Mandatory Access Control come SELinux.

## Punti chiave

- File utenti: **/etc/passwd** (`Name:PWD:UID:GID:Full name:Home:Shell`), **/etc/group** (`GroupName:PWD:GID:UID1:UID2...`), **/etc/shadow** (password cifrate). Definiscono UID e GID.
- Account speciali: **root** (amministratore, *solo* UID=0; rinominabile), **nobody**, account built-in (mail, sys, bin). Non basta appartenere al gruppo root per avere i privilegi.
- Comandi: `adduser`, info utenti connessi (`w`, `who`, `last`, `users`, `finger`), cambio identità `su`/`su -`, `sudo` (versione evoluta), `whoami`, `chage` (scadenza password).
- **Profili utente**: file nascosti nella HOME; ordine globale (`/etc/profile`) poi per-utente (`.bash_profile`); limiti via `ulimit`, `/etc/security`. Script di login/logout.
- **Permessi filesystem**: 9 bit `rwxrwxrwx` + bit speciali (setuid/setgid/sticky), owner (UID) e gruppo (GID). Alternative: **ACL**, attributi estesi, **security label**. Ogni processo assume UID/GID dell'utente.
- **Autenticazione**: locale (login/password cifrate) o centralizzata (dominio **NIS** con pacchetti yp*, dominio **NT/AD** via **SAMBA**, **LDAP** via OpenLDAP).

## PAM e NSS

- **PAM** (Pluggable Authentication Modules): permette di sviluppare programmi indipendenti dal tipo di autenticazione e di riconfigurarla. `login`/`telnet`/`ftp` → `libpam` → moduli PAM → host locale/remoto.
- **NSS** (Name Service Switch): la libreria NSS specifica come risolvere i "nomi" (`/etc/nsswitch.conf`); file che possono diventare "dinamici": group, hosts, network, passwd, protocols, services, shadow.

## Gestione avanzata e SELinux

Impostazioni avanzate: quote di utilizzo del filesystem, priorità CPU, tempi terminali, accounting, shadow password con cracklib (password a tempo, check, riuso da `/etc/security/opasswd`).

**SELinux** implementa Mandatory Access Control: un *Security Server* valuta le richieste di un *Subject* (processo) su un *Object* (file) consultando la *Policy Database*; gli utenti Unix mappano a identità SELinux → ruoli → dominio (es. `sysadm_r`/`sysadm_t`, `user_r`/`user_t`). Tutti i processi e i file hanno una **security label** (contesto SELinux), visibile con `ls -Z` (es. `unconfined_u:object_r:user_home_t:s0`).

## Articoli correlati

- [[server-linux]]
- [[linux-gestione-sistema]]
- [[linux-filesystem-storage]]
- [[sicurezza-di-sistema]]

## Fonti

- `raw/2 - ServerLinux_COMPILED.pdf` — slide del corso *Amministrazione di Sistema* (UniBS, 2024-25), modulo "Server Linux", sezioni gestione utenti, permessi, autenticazione (NIS/LDAP/PAM/NSS), SELinux.
