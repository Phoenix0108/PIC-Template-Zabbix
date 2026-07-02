# PIC Synology DiskStation

> Template d'espace disque

## Vue d'ensemble

| Propriété | Valeur |
|---|---|
| Nom technique | `PIC Synology DiskStation` |
| Nom d'affichage | PIC Synology DiskStation |
| Version Zabbix | 7.4 |
| Fichier | `PIC Synology DiskStation.yaml` |
| Groupes | PIC informatique - Template, Synology, Templates |
| Éléments (items) | 24 |
| Règles de découverte | 9 |
| Prototypes d'éléments | 35 |
| Déclencheurs | 9 |
| Macros | 4 |
| Cartographies de valeurs | 6 |
| Tableaux de bord | 0 |

## Description

Template d'espace disque

## Macros

| Macro | Valeur par défaut | Description |
|---|---|---|
| `{$DISK_UTIL_HIGH}` | 95 |  |
| `{$DISK_UTIL_WARN}` | 85 |  |
| `{$SNMP_COMMUNITY}` | picinformatique |  |
| `{$SNMP_UNAVAIL_WARN}` | 5m | Durée d'indisponibilité SNMP continue avant de considérer la collecte SNMP en échec (utilisée par les alertes de corrélation SNMP/ICMP). |

## Éléments surveillés (items)

| Nom | Clé | Type | Type de valeur | Unité |
|---|---|---|---|---|
| CPU Fan Status | `synoSystem.cpuFanStatus` | SNMP_AGENT | UNSIGNED |  |
| CPU Idle | `synoSystem.ssCpuIdle` | SNMP_AGENT | UNSIGNED | % |
| CPU System | `synoSystem.ssCpuSystem` | SNMP_AGENT | UNSIGNED | % |
| CPU User | `synoSystem.ssCpuUser` | SNMP_AGENT | UNSIGNED | % |
| Load Avg 1 min | `synoSystem.laLoadInt.1` | SNMP_AGENT | FLOAT |  |
| Load Avg 15 min | `synoSystem.laLoadInt.3` | SNMP_AGENT | FLOAT |  |
| Load Avg 5 min | `synoSystem.laLoadInt.2` | SNMP_AGENT | FLOAT |  |
| Model Name | `synoSystem.modelName` | SNMP_AGENT | TEXT |  |
| Power Status | `synoSystem.powerStatus` | SNMP_AGENT | UNSIGNED |  |
| Serial Number | `synoSystem.serialNumber` | SNMP_AGENT | TEXT |  |
| SNMP agent availability | `zabbix[host,snmp,available]` | INTERNAL | UNSIGNED |  |
| System Fan Status | `synoSystem.systemFanStatus` | SNMP_AGENT | UNSIGNED |  |
| System Status | `synoSystem.systemStatus` | SNMP_AGENT | UNSIGNED |  |
| System Temperature | `synoSystem.temperature` | SNMP_AGENT | UNSIGNED | °C |
| Total Buffer Memory | `ucdavis.memory.memBuffer` | SNMP_AGENT | UNSIGNED | B |
| Total Cached Memory | `ucdavis.memory.memCached` | SNMP_AGENT | UNSIGNED | B |
| Total Free Memory | `ucdavis.memory.memTotalFree` | SNMP_AGENT | UNSIGNED | B |
| Total Physical Available | `ucdavis.memory.memAvailReal` | SNMP_AGENT | UNSIGNED | B |
| Total Physical Memory | `ucdavis.memory.memTotalReal` | SNMP_AGENT | UNSIGNED | B |
| Total Shared Memory | `ucdavis.memory.memShared` | SNMP_AGENT | UNSIGNED | B |
| Total Swap Available | `ucdavis.memory.memAvailSwap` | SNMP_AGENT | UNSIGNED | B |
| Total Swap Space | `ucdavis.memory.memTotalSwap` | SNMP_AGENT | UNSIGNED | B |
| Upgrade Available | `synoSystem.upgradeAvailable` | SNMP_AGENT | UNSIGNED |  |
| Version | `synoSystem.version` | SNMP_AGENT | TEXT |  |

## Règles de découverte (LLD)

| Nom | Clé | Type | Prototypes d'éléments | Prototypes de déclencheurs |
|---|---|---|---|---|
| Disk | `host.hrStorage.hrStorageTable.hrStorageEntry` | SNMP_AGENT | 3 | 0 |
| Network | `ifMIB.ifMIBObjects.ifXTable.ifXEntry` | SNMP_AGENT | 2 | 0 |
| Synology Disk(s) | `synoDisk.diskTable.diskEntry` | SNMP_AGENT | 5 | 0 |
| Synology Disk(s) SMART | `synologyDiskSMART.diskSMARTTable.diskSMARTEntry` | SNMP_AGENT | 1 | 0 |
| Synology iSCSI LUN | `synologyiSCSILUN.iSCSILUNTable.iSCSILUNEntry` | SNMP_AGENT | 11 | 0 |
| Synology RAID Volume(s) | `synoRaid.raidTable.raidEntry` | SNMP_AGENT | 2 | 0 |
| Synology Service(s) | `synologyService.serviceTable.serviceEntry` | SNMP_AGENT | 1 | 0 |
| Synology SpaceIO | `spaceIO.spaceIOTable.spaceIOEntry` | SNMP_AGENT | 5 | 0 |
| Synology StorageIO | `storageIO.storageIOTable.storageIOEntry` | SNMP_AGENT | 5 | 0 |

## Déclencheurs (triggers)

| Nom | Sévérité | Description |
|---|---|---|
| System Partition CRITICAL on {HOST.NAME} | Désastre |  |
| CPU Fan CRITICAL on {HOST.NAME} | Élevé |  |
| CPU Usage Very High on {HOST.NAME} | Élevé |  |
| Synology: SNMP inaccessible | Moyen | La collecte SNMP est en échec depuis plus de {$SNMP_UNAVAIL_WARN}, alors qu'elle fonctionnait auparavant. Corrélation ICMP à finaliser : ce déclencheur devra dépendre de « Unavailable by ICMP ping » du template ICMP pour être suppr. lors d'une panne totale du NAS. |
| System Temperature CRITICAL on {HOST.NAME} | Moyen |  |
| CPU Usage High on {HOST.NAME} | Avertissement |  |
| Power Status FAILED on {HOST.NAME} | Avertissement |  |
| System Fan Status CRITCAL on {HOST.NAME} | Avertissement |  |
| Update available on {HOST.NAME} | Information |  |

## Cartographies de valeurs (value maps)

| Nom | Correspondances |
|---|---|
| SYNOLOGY-DISK-MIB::diskHealthStatus | 1→Normal, 2→Warning, 3→Critical, 4→Failing |
| SYNOLOGY-DISK-MIB::diskStatus | 1→Normal, 2→Initialized, 3→Not Initialized, 4→System Partition Failed, 5→Crashed |
| SYNOLOGY-RAID-MIB::raidStatus | 1→Normal, 2→Repairing, 3→Migrating, 4→Expanding, 5→Deleting, 6→Creating, 7→Raid Syncing, 8→Raid Parity Checking, … (+4) |
| SYNOLOGY-SYSTEM-MIB::powerStatus | 1→Normal, 2→Failed |
| SYNOLOGY-SYSTEM-MIB::systemStatus | 1→Normal, 2→Failed |
| SYNOLOGY-SYSTEM-MIB::upgradeAvailable | 1→Available, 2→Unavailable, 3→Connecting, 4→Disconnected, 5→Others |

---
*Documentation générée automatiquement à partir de l'export Zabbix.*
