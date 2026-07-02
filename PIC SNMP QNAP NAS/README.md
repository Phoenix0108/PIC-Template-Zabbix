# PIC SNMP QNAP NAS

## Vue d'ensemble

| Propriété | Valeur |
|---|---|
| Nom technique | `PIC SNMP QNAP NAS` |
| Nom d'affichage | PIC SNMP QNAP NAS |
| Version Zabbix | 7.4 |
| Fichier | `PIC SNMP QNAP NAS.yaml` |
| Groupes | PIC informatique - Template, Templates |
| Éléments (items) | 13 |
| Règles de découverte | 4 |
| Prototypes d'éléments | 13 |
| Déclencheurs | 4 |
| Macros | 2 |
| Cartographies de valeurs | 0 |
| Tableaux de bord | 0 |

## Macros

| Macro | Valeur par défaut | Description |
|---|---|---|
| `{$HD_TEMPERATURE_ALARM}` | 51 |  |
| `{$HD_TEMPERATURE_WARNING}` | 40 |  |

## Éléments surveillés (items)

| Nom | Clé | Type | Type de valeur | Unité |
|---|---|---|---|---|
| CPU temperature | `CPU-TemperatureEX` | SNMP_AGENT | UNSIGNED | C |
| CPU usage | `SystemCPU-UsageEX` | SNMP_AGENT | UNSIGNED | % |
| Device firmware revision | `PhysicalFirmwareRev` | SNMP_AGENT | TEXT |  |
| Device hardware revision | `PhysicalHardwareRev` | SNMP_AGENT | TEXT |  |
| Device model name | `ModelNameEX` | SNMP_AGENT | TEXT |  |
| Device software revision | `PhysicalSoftwareRev` | SNMP_AGENT | TEXT |  |
| Device system name | `HostNameEX` | SNMP_AGENT | TEXT |  |
| Device vendor name | `sysPhysicalMfgName` | SNMP_AGENT | TEXT |  |
| System free memory | `SystemFreeMemEX` | SNMP_AGENT | UNSIGNED | b |
| System free memory (%) | `SystemPFreeMemEX` | CALCULATED | FLOAT | % |
| System temperature | `SystemTemperatureEX` | SNMP_AGENT | UNSIGNED | C |
| System total memory | `SystemTotalMemEX` | SNMP_AGENT | UNSIGNED | b |
| System uptime | `sysUptime` | SNMP_AGENT | UNSIGNED | uptime |

## Règles de découverte (LLD)

| Nom | Clé | Type | Prototypes d'éléments | Prototypes de déclencheurs |
|---|---|---|---|---|
| Hard disks discovery | `sysHDs.discovery` | SNMP_AGENT | 5 | 0 |
| Network interfaces discovery | `netIfs.discovery` | SNMP_AGENT | 3 | 0 |
| System fans discovery | `sysFans.discovery` | SNMP_AGENT | 1 | 0 |
| Volumes discovery | `sysVolumes.discovery` | SNMP_AGENT | 4 | 0 |

## Déclencheurs (triggers)

| Nom | Sévérité | Description |
|---|---|---|
| {HOST.NAME} has just been restarted | Élevé |  |
| CPU utilization on {HOST.NAME} is over 95% for 15 min | Moyen |  |
| CPU utilization on {HOST.NAME} is over 80% for 15 min | Avertissement |  |
| Firmware on {HOST.NAME} is changed | Information |  |

---
*Documentation générée automatiquement à partir de l'export Zabbix.*
