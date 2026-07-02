# PIC Ubiquiti Unifi Network AP by SNMP

> ## Description

## Vue d'ensemble

| Propriété | Valeur |
|---|---|
| Nom technique | `PIC Ubiquiti Unifi Network AP by SNMP` |
| Nom d'affichage | PIC Ubiquiti Unifi Network AP by SNMP |
| Version Zabbix | 7.4 |
| Fichier | `PIC Ubiquiti Unifi Network AP by SNMP.yaml` |
| Groupes | PIC informatique - Template, Templates/Network devices |
| Éléments (items) | 27 |
| Règles de découverte | 1 |
| Prototypes d'éléments | 9 |
| Déclencheurs | 8 |
| Macros | 4 |
| Cartographies de valeurs | 0 |
| Tableaux de bord | 1 |

## Description

## Description

This template uses SNMP data from Unifi AP devices. MIBS requirements: FROGFOOT-RESOURCES-MIB IANAifType-MIB IF-MIB SNMP-FRAMEWORK-MIB SNMPv2-CONF SNMPv2-MIB SNMPv2-SMI SNMPv2-TC UBNT-MIB UBNT-UniFi-MIB Author: Helmut Leonhardt based on Alex Moura

## Overview


unifi zabbix snmpv3
based on Alex Mouras work https://share.zabbix.com/network\_devices/ubiquiti/unifi-snmp-2019-v1-1


Ubiquity Controller > Settings > Services > SNMP v3 > enable, set username and password
Zabbix > Macro >
{$SNMP\_USERNAME} <= username set in controller
{$SNMP\_AUTHPASS} & {$SNMP\_PRIVPASS} <= password set in controller

2020 April
fixed zabbix version
fixed virtual interface items

2024 September - Fixes by Crowtrobot
Added network traffic numbers in bits/sec to simplify comparing to other network devices.
Replaced some description strings that appeared to be in Portuguese with English strings, or removed them completely where they didn't seem useful.
Fixed 5GHz channel oid, which was wrong on my UAP-AC-InWall and U6-Enterprise.
Added 6GHz radio channel oid.


## Author

Helmut Leonhardt

## Macros

| Macro | Valeur par défaut | Description |
|---|---|---|
| `{$SNMP_AUTHPASS}` |  |  |
| `{$SNMP_PORT}` | 161 |  |
| `{$SNMP_PRIVPASS}` |  |  |
| `{$SNMP_USERNAME}` |  |  |

## Éléments surveillés (items)

| Nom | Clé | Type | Type de valeur | Unité |
|---|---|---|---|---|
| AP Hostname | `unifiSysName.0` | SNMP_AGENT | TEXT |  |
| Channel 2G (N/G) | `unifiVapChannel.1` | SNMP_AGENT | UNSIGNED |  |
| Channel 5G (N/AC) | `unifiVapChannel.5` | SNMP_AGENT | UNSIGNED |  |
| Channel 6G (N/AC) | `unifiVapChannel.6` | SNMP_AGENT | UNSIGNED |  |
| Channel utilization 2G (BGN) | `unifiRadioCuTotal.1` | SNMP_AGENT | UNSIGNED | % |
| Channel utilization 5G (AC) | `unifiRadioCuTotal.2` | SNMP_AGENT | UNSIGNED | % |
| Channel utilization 6G (AC) | `unifiRadioCuTotal.3` | SNMP_AGENT | UNSIGNED | % |
| Contact | `sysContact.0` | SNMP_AGENT | TEXT |  |
| CPU AVG Load 1 Min | `IaLoad.1` | SNMP_AGENT | FLOAT |  |
| CPU AVG Load 15 Min | `IaLoad.3` | SNMP_AGENT | FLOAT |  |
| CPU AVG Load 5 Min | `IaLoad.2` | SNMP_AGENT | FLOAT |  |
| CPU Usage | `cpuLoad.0` | SNMP_AGENT | UNSIGNED | % |
| Firmware version | `unifiApSystemVersion.0` | SNMP_AGENT | TEXT |  |
| Interface Speed (Mbit/s) | `unifiIfSpeed.1` | SNMP_AGENT | UNSIGNED |  |
| IP Address | `unifiApSystemIp.0` | SNMP_AGENT | TEXT |  |
| LAN Traffic Incoming (bits) | `unifiIfRxBits.1` | SNMP_AGENT | UNSIGNED | bps |
| LAN Traffic Incoming (bytes) | `unifiIfRxBytes.1` | SNMP_AGENT | UNSIGNED | Bps |
| LAN Traffic Incoming Errors | `unifiIfRxError.1` | SNMP_AGENT | UNSIGNED | Error(s) |
| LAN Traffic Outgoing (bits) | `unifiIfTxBits.1` | SNMP_AGENT | UNSIGNED | bps |
| LAN Traffic Outgoing (bytes) | `unifiIfTxBytes.1` | SNMP_AGENT | UNSIGNED | Bps |
| LAN Traffic Outgoing Errors | `unifiIfTxError.1` | SNMP_AGENT | UNSIGNED | Error(s) |
| Location | `sysLocation.0` | SNMP_AGENT | TEXT |  |
| MAC Address | `unifiIfMac.1` | SNMP_AGENT | TEXT |  |
| Model | `unifiApSystemModel.0` | SNMP_AGENT | TEXT |  |
| System Description | `sysDescr.0` | SNMP_AGENT | TEXT |  |
| System Time | `hrSystemDate.0` | SNMP_AGENT | TEXT |  |
| Uptime | `sysUpTime.0` | SNMP_AGENT | FLOAT | uptime |

## Règles de découverte (LLD)

| Nom | Clé | Type | Prototypes d'éléments | Prototypes de déclencheurs |
|---|---|---|---|---|
| Wifi Virtual Interfaces | `unifiVapEssId` | SNMP_AGENT | 9 | 0 |

## Déclencheurs (triggers)

| Nom | Sévérité | Description |
|---|---|---|
| Processor load is very high on {HOST.NAME} | Élevé |  |
| Channel utilization 2G on {HOST.NAME} very high | Moyen |  |
| Channel utilization 5G on {HOST.NAME} very high | Moyen |  |
| Channel utilization 6G on {HOST.NAME} very high | Moyen |  |
| Processor load is too high on {HOST.NAME} | Moyen |  |
| Channel utilization 2G on {HOST.NAME} high | Avertissement |  |
| Channel utilization 5G on {HOST.NAME} high | Avertissement |  |
| Channel utilization 6G on {HOST.NAME} high | Avertissement |  |

## Tableaux de bord (dashboards)

- Overview

---
*Documentation générée automatiquement à partir de l'export Zabbix.*
