# PIC - Dell EqualLogic PS by SNMP

> Template de monitoring pour baies Dell EqualLogic PS Series (ex : PS4100XV / 70-0476)

## Vue d'ensemble

| Propriété | Valeur |
|---|---|
| Nom technique | `PIC - Dell EqualLogic PS by SNMP` |
| Nom d'affichage | PIC - Dell EqualLogic PS by SNMP |
| Version Zabbix | 7.4 |
| Fichier | `PIC - Dell EqualLogic PS by SNMP.yaml` |
| Groupes | PIC informatique - Template, Templates/Storage |
| Éléments (items) | 19 |
| Règles de découverte | 4 |
| Déclencheurs | 8 (+ prototypes) |
| Macros | 5 |
| Cartographies de valeurs | 4 |
| Graphiques | 3 |
| Tags | class: storage, target: SAN, vendor: Dell |

## Description

Template SNMP v2c pour les baies **Dell EqualLogic PS Series** (testé pour le modèle **PS4100XV**, part number **70-0476**).
Il s'appuie sur les MIB **EQLMEMBER-MIB** et **EQLDISK-MIB** (entreprise `1.3.6.1.4.1.12740`).

- Santé du membre et statut RAID
- Capacité (totale, utilisée, snapshots, réplication) + pourcentage d'utilisation calculé
- Latence et IOPS lecture/écriture
- Découverte des disques physiques, capteurs de température, ventilateurs et alimentations

> **Note multi-membres** : les éléments scalaires ciblent l'index membre `.1` (baie mono-membre, cas du PS4100XV).
> Pour un groupe EqualLogic multi-membres, dupliquer les items en remplaçant l'index `.1` par l'index du membre concerné.

Protocole : SNMP v2c
Auteur     : PIC informatique
Version    : 1.0 — Zabbix 7.4

## Prérequis

1. Activer SNMP sur le groupe EqualLogic (Group Manager → Group Configuration → SNMP).
2. Renseigner la communauté SNMP en lecture dans la macro `{$SNMP_COMMUNITY}`.
3. Associer une interface **SNMP** sur l'hôte Zabbix pointant vers l'IP de gestion du groupe.

## Macros

| Macro | Valeur par défaut | Description |
|---|---|---|
| `{$SNMP_COMMUNITY}` | picinformatique | Communauté SNMP v2c |
| `{$STORAGE_UTIL_HIGH}` | 90 | Seuil critique d'utilisation du stockage (%) |
| `{$STORAGE_UTIL_WARN}` | 80 | Seuil d'alerte d'utilisation du stockage (%) |
| `{$TEMP_CRIT}` | 60 | Température critique (°C) |
| `{$TEMP_WARN}` | 50 | Température d'alerte (°C) |

## Éléments surveillés (items)

| Nom | OID | Unité |
|---|---|---|
| ICMP Ping / Loss / Response Time | — | %, ms |
| Model Name | `.1.3.6.1.4.1.12740.2.1.11.1.1.1` | — |
| Serial Number | `.1.3.6.1.4.1.12740.2.1.11.1.2.1` | — |
| Member Name | `.1.3.6.1.4.1.12740.2.1.1.1.9.1` | — |
| Number of Controllers | `.1.3.6.1.4.1.12740.2.1.11.1.3.1` | — |
| Number of Disks | `.1.3.6.1.4.1.12740.2.1.11.1.4.1` | — |
| Member Health Status | `.1.3.6.1.4.1.12740.2.1.5.1.1.1` | valuemap |
| RAID Status | `.1.3.6.1.4.1.12740.2.1.13.1.1.1` | valuemap |
| Total Storage | `.1.3.6.1.4.1.12740.2.1.10.1.1.1` | B |
| Used Storage | `.1.3.6.1.4.1.12740.2.1.10.1.2.1` | B |
| Snapshot Storage | `.1.3.6.1.4.1.12740.2.1.10.1.3.1` | B |
| Replication Storage | `.1.3.6.1.4.1.12740.2.1.10.1.4.1` | B |
| Storage Used (%) | calculé | % |
| Read / Write Latency | `.1.3.6.1.4.1.12740.2.1.12.1.4.1` / `.5.1` | ms |
| Read / Write Operations | `.1.3.6.1.4.1.12740.2.1.12.1.6.1` / `.7.1` | ops |

> Les capacités sont exposées en Mo par la MIB et converties en octets (× 1 048 576).

> **Étiquetage disque fiable** : le slot physique est relu en direct au même index que le statut et affiché
> dans l'`opdata` des alertes disque, afin que l'alerte désigne toujours le bon disque même si l'index de la
> table venait à glisser. La découverte tourne toutes les 30 min.

## Règles de découverte (LLD)

| Nom | OID de découverte | Prototypes |
|---|---|---|
| Physical Disks | `1.3.6.1.4.1.12740.3.1.1.1.10` | Status, Slot (live), Size |
| Temperature Sensors | `1.3.6.1.4.1.12740.2.1.6.1.2` | Value |
| Fans | `1.3.6.1.4.1.12740.2.1.7.1.2` | Speed |
| Power Supplies | `1.3.6.1.4.1.12740.2.1.8.1.2` | Status |

## Déclencheurs (triggers)

| Nom | Sévérité |
|---|---|
| RAID has FAILED | Désastre |
| Member health is CRITICAL | Élevé |
| RAID is DEGRADED | Élevé |
| Storage usage is CRITICAL | Élevé |
| Disk {#DISKSLOT} has FAILED | Élevé |
| Power Supply {#PSU} problem | Élevé |
| Temperature {#SENSOR} is CRITICAL | Élevé |
| Member health is WARNING | Moyen |
| Disk off-line/unhealthy | Moyen |
| Fan {#FAN} stopped | Moyen |
| RAID is reconstructing | Avertissement |
| Storage usage is HIGH | Avertissement |
| Temperature {#SENSOR} is HIGH | Avertissement |
| {HOST.NAME} is unreachable | Élevé |
| Packet loss HIGH | Avertissement |

## Cartographies de valeurs (value maps)

| Nom | Correspondances |
|---|---|
| EQL Member Health Status | 0→unknown, 1→normal, 2→warning, 3→critical |
| EQL RAID Status | 1→ok, 2→degraded, 3→verifying, 4→reconstructing, 5→failed, 6→catastrophic-loss, 7→expanding, 8→mirroring |
| EQL Disk Status | 1→on-line, 2→spare, 3→failed, 4→off-line, … 9→unhealthy, 10→replacement |
| EQL Power Supply Status | 1→on-and-operating, 2→no-ac-power, 3→failed-or-no-data |

---
*Documentation générée à partir de l'export Zabbix. OID issus des MIB EQLMEMBER-MIB / EQLDISK-MIB (Dell EqualLogic, entreprise 12740).*
