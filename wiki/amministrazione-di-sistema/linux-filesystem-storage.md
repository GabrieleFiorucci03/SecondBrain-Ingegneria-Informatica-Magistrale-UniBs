---
tags: [amministrazione-di-sistema, linux, filesystem, lvm, ext4, xfs, backup]
data_creazione: 2026-05-28
data_aggiornamento: 2026-05-28
fonti:
  - raw/2 - ServerLinux_COMPILED.pdf
---

# Filesystem e storage in Linux

Linux organizza file e directory in una **struttura gerarchica** unica (radice `/`) conforme al **FHS**, su cui si innestano filesystem diversi tramite mount. L'amministratore sceglie filesystem, gestisce dischi e volumi (LVM), e implementa archivi e backup.

## Punti chiave

- **Tipi di file**: convenzionali (il formato non dipende dall'estensione — filosofia Unix), directory, link (hard/soft), device a caratteri/blocchi, elementi IPC (socket Unix, FIFO).
- **Filesystem nativi**: ext2 (tradizionale), ext3/ext4/ReiserFS/XFS/JFS (journaling), Btrfs, ZFS, con attributi estesi e POSIX ACL. Filesystem "di altri mondi" (iso9660, vfat, ntfs), di rete (NFS, SMB, CODA), di terze parti (FTP, sshfs, WebDAV), in loopback, in user-mode (**FUSE**).
- Scelta: filesystem **maturi** (Ext4, XFS) vs **tech preview** (Btrfs, ZFS). Da RHEL 7.0 **XFS** è il default al posto di ext4. Tuning: block size, access date (noatime/relatime), journal mode, write barrier.
- **mount** innesta un filesystem nell'altro: lavorare con più filesystem si riduce a un unico albero. `mount -o opz -t fs device dir`; automatizzazione via **/etc/fstab**; alternative supermount/automounter/autofs.
- Operazioni FS: `mkfs` (crea), `sync` (svuota cache), check (fsck/`xfs_repair`), analisi spazio (`df`, `du`), resize (`resize2fs` per ext4, `xfs_growfs` per XFS), space reclaim (`fstrim` per SSD/thin provisioning).
- **Backup**: compressione (`gzip`, `bzip2`, `zstd`, `xz`), archiviazione (`tar`, `cpio`, `dump`), su nastro (`/dev/st0`, `mt`). Cosa salvare: configurazioni, dati, sistema completo (disaster recovery). Definire supporto e politica di backup.

## LVM (Logical Volume Manager)

LVM è un oggetto astratto costruito su partizioni (o dischi raw), standard in tutte le distribuzioni. Componenti: **PV** (Physical Volume, partizione/disco), **VG** (Volume Group, insieme di PV usati come un unico disco), **LV** (Logical Volume, partizione virtuale), **PE** (Physical Extent, porzione ~4MB). Stack: Physical Drives → Partitions → Physical Volumes → Volume Group → Logical Volume → Filesystem. Permette ridimensionamento flessibile dei volumi.

## XFS vs ext4 e layer avanzati

Tabella di corrispondenza comandi: creazione `mkfs.ext4`/`mkfs.xfs`, check `e2fsck`/`xfs_repair`, resize `resize2fs`/`xfs_growfs`, image `e2image`/`xfs_metadump`, tuning `tune2fs`/`xfs_admin`, backup `dump`/`xfsdump`. Limiti XFS: non riducibile, crescibile online. **FUSE** consente filesystem in user space (NTFS-3G, EncFS, GlusterFS, Linear Tape FS). **VDO** (Virtual Data Optimizer, da RHEL 7.5): layer trasparente di compressione/deduplica nel kernel.

## Gestione dei dischi

Inizializzazione: **fdisk** (MBR), **parted** (GPT). Poi partizionamento (partizioni LVM o native ext4/xfs), gestione volumi, formattazione, configurazione `/etc/fstab`.

## Articoli correlati

- [[server-linux]]
- [[linux-installazione-boot]]
- [[linux-gestione-sistema]]
- [[storage-server]]

## Fonti

- `raw/2 - ServerLinux_COMPILED.pdf` — slide del corso *Amministrazione di Sistema* (UniBS, 2024-25), modulo "Server Linux", sezioni filesystem, LVM, ext4/XFS, FUSE/VDO, gestione dischi, backup.
