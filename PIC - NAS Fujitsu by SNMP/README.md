# PIC - NAS Fujitsu by SNMP

> Template de monitoring pour NAS Fujitsu (CELVIN, ETERNUS NR, etc.)

## Vue d'ensemble

| Propriété | Valeur |
|---|---|
| Nom technique | `PIC - NAS Fujitsu by SNMP` |
| Nom d'affichage | PIC - NAS Fujitsu by SNMP |
| Version Zabbix | 7.4 |
| Fichier | `PIC - NAS Fujitsu by SNMP.yaml` |
| Groupes | PIC informatique - Template, Templates/Storage |
| Éléments (items) | 26 |
| Règles de découverte | 3 |
| Prototypes d'éléments | 7 |
| Déclencheurs | 8 |
| Macros | 4 |
| Cartographies de valeurs | 2 |
| Tableaux de bord | 0 |
| Tags | class: storage, target: NAS |

## Description

Template de monitoring pour NAS Fujitsu (CELVIN, ETERNUS NR, etc.)
Protocole : SNMP v2c
Auteur     : PIC informatique
Version    : 2.0 — Zabbix 7.4

## Macros

| Macro | Valeur par défaut | Description |
|---|---|---|
| `{$DISK_UTIL_HIGH}` | 95 | Seuil critique espace disque (%) |
| `{$DISK_UTIL_WARN}` | 85 | Seuil d'alerte espace disque (%) |
| `{$SNMP_COMMUNITY}` | picinformatique | Communauté SNMP v2c |
| `{$TEMP_CRIT}` | 60 | Température critique (°C) |

## Éléments surveillés (items)

| Nom | Clé | Type | Type de valeur | Unité |
|---|---|---|---|---|
| CPU Fan Status | `sensor.fan.speed[lmFanSensorsValue.1]` | SNMP_AGENT | UNSIGNED | rpm |
| CPU Idle | `system.cpu.idle[ssCpuIdle]` | SNMP_AGENT | UNSIGNED | % |
| CPU System | `system.cpu.system[ssCpuSystem]` | SNMP_AGENT | UNSIGNED | % |
| CPU Temperature | `sensor.temp.value[lmTempSensorsValue.2]` | SNMP_AGENT | UNSIGNED | °C |
| CPU User | `system.cpu.user[ssCpuUser]` | SNMP_AGENT | UNSIGNED | % |
| ICMP Loss | `icmppingloss` | SIMPLE | UNSIGNED | % |
| ICMP Ping | `icmpping` | SIMPLE | UNSIGNED |  |
| ICMP Response Time | `icmppingsec` | SIMPLE | UNSIGNED | ms |
| Load Avg 1 min | `system.cpu.load[laLoadInt.1]` | SNMP_AGENT | FLOAT |  |
| Load Avg 15 min | `system.cpu.load[laLoadInt.3]` | SNMP_AGENT | FLOAT |  |
| Load Avg 5 min | `system.cpu.load[laLoadInt.2]` | SNMP_AGENT | FLOAT |  |
| Model Name | `system.descr[sysDescr.0]` | SNMP_AGENT | TEXT |  |
| Number of Processes | `proc.num[hrSystemProcesses]` | SNMP_AGENT | UNSIGNED |  |
| Number of Users | `system.users.num[hrSystemNumUsers]` | SNMP_AGENT | UNSIGNED |  |
| Serial Number / Hostname | `system.hostname[sysName.0]` | SNMP_AGENT | TEXT |  |
| System Fan Status | `sensor.fan.speed[lmFanSensorsValue.2]` | SNMP_AGENT | UNSIGNED | rpm |
| System Status | `system.status[hrSystemUptime.0]` | SNMP_AGENT | UNSIGNED |  |
| System Temperature | `sensor.temp.value[lmTempSensorsValue.1]` | SNMP_AGENT | UNSIGNED | °C |
| System Uptime | `system.uptime[sysUpTime.0]` | SNMP_AGENT | UNSIGNED | uptime |
| Total Buffer Memory | `ucdavis.memory.memBuffer` | SNMP_AGENT | UNSIGNED | B |
| Total Cached Memory | `ucdavis.memory.memCached` | SNMP_AGENT | UNSIGNED | B |
| Total Free Memory | `ucdavis.memory.memTotalFree` | SNMP_AGENT | UNSIGNED | B |
| Total Physical Available | `ucdavis.memory.memAvailReal` | SNMP_AGENT | UNSIGNED | B |
| Total Physical Memory | `ucdavis.memory.memTotalReal` | SNMP_AGENT | UNSIGNED | B |
| Total Swap Available | `ucdavis.memory.memAvailSwap` | SNMP_AGENT | UNSIGNED | B |
| Total Swap Space | `ucdavis.memory.memTotalSwap` | SNMP_AGENT | UNSIGNED | B |

## Règles de découverte (LLD)

| Nom | Clé | Type | Prototypes d'éléments | Prototypes de déclencheurs |
|---|---|---|---|---|
| Disk | `host.hrStorage.hrStorageTable.hrStorageEntry` | SNMP_AGENT | 3 | 0 |
| Network | `ifMIB.ifMIBObjects.ifXTable.ifXEntry` | SNMP_AGENT | 3 | 0 |
| Physical Disk(s) | `host.hrDevice.hrDiskStorageTable` | SNMP_AGENT | 1 | 0 |

## Déclencheurs (triggers)

| Nom | Sévérité | Description |
|---|---|---|
| CPU Fan CRITICAL on {HOST.NAME} | Élevé |  |
| CPU Usage Very High on {HOST.NAME} | Élevé |  |
| {HOST.NAME} is unreachable | Élevé |  |
| System Temperature CRITICAL on {HOST.NAME} | Moyen |  |
| CPU Usage High on {HOST.NAME} | Avertissement |  |
| Packet loss HIGH on {HOST.NAME} | Avertissement |  |
| System Fan Status CRITICAL on {HOST.NAME} | Avertissement |  |
| System restarted recently on {HOST.NAME} | Avertissement |  |

## Cartographies de valeurs (value maps)

| Nom | Correspondances |
|---|---|
| HOST-RESOURCES-MIB::hrDeviceStatus | 1→unknown, 2→running, 3→warning, 4→testing, 5→down |
| IF-MIB::ifOperStatus | 1→up, 2→down, 3→testing, 4→unknown, 5→dormant, 6→notPresent, 7→lowerLayerDown |

---
*Documentation générée automatiquement à partir de l'export Zabbix.*
