# PIC Ubiquiti Firewall by SNMP

> Overview

## Vue d'ensemble

| Propriété | Valeur |
|---|---|
| Nom technique | `PIC Ubiquiti Firewall by SNMP` |
| Nom d'affichage | PIC Ubiquiti Firewall by SNMP |
| Version Zabbix | 7.4 |
| Fichier | `PIC Ubiquiti Firewall by SNMP.yaml` |
| Groupes | Firewall, PIC informatique - Template |
| Éléments (items) | 12 |
| Règles de découverte | 14 |
| Prototypes d'éléments | 61 |
| Déclencheurs | 4 |
| Macros | 26 |
| Cartographies de valeurs | 2 |
| Tableaux de bord | 1 |
| Tags | Author: Simon Jackson, Vendor: Ubiquiti |

## Description

Overview
This template monitors Ubiquiti UniFi Security Gateway (USG), UniFi Dream Machine (UDM), and EdgeRouter series devices using SNMPv2.
It provides metrics on system health, interfaces, CPU, memory, active sessions, and basic traffic statistics.

Features
- Device identification (model, name, location, uptime)
- CPU and memory utilisation tracking
- Interface traffic (in/out) and operational status
- Active session count (established connections)
- SNMP-based discovery of physical interfaces
- Basic triggers for CPU/memory overuse and interface state change
- Graphs for bandwidth and session statistics

Items
- System info (uptime, hostname, description)
- CPU and memory usage
- Active session count
- Per-interface byte counters and status

Discovery rules
- Physical interface discovery using `ifDescr` and `ifOperStatus`

Item prototypes
- Interface in/out bytes per second
- Interface operational status

Trigger prototypes
- Interface down alert
- High CPU/memory usage alerts (optional based on thresholds)

Graph prototypes
- Interface traffic overview (in/out)
- Session count trends

Supported devices
- Ubiquiti UniFi Security Gateway (USG/USG-Pro)
- Ubiquiti EdgeRouter series (ER-X, ER-4, ER-12, etc.)
- UniFi Dream Machine (basic support, SNMP limitations apply)

Notes
- Requires SNMP enabled on the device and allowed from the Zabbix proxy/server
- Interface counters are in octets and converted to bits per second
- SNMP agent responses may vary slightly across firmware or product lines

Author: Simon Jackson / @sjackson0109

## Macros

| Macro | Valeur par défaut | Description |
|---|---|---|
| `{$UBIQUITI_CPU_UTIL_MAX}` | 90 | Maximum allowed CPU usage (%) before raising warning. |
| `{$UBIQUITI_INTERFACE_ERROR_THRESHOLD}` | 10 | Interface {#IFDESCR} is experiencing a high number of SEND errors. This may indicate bad cabling, duplex mismatch, or hardware issues. |
| `{$UBIQUITI_MEMORY_USED_MAX}` | 95 | Threshold for raising memory usage alerts. |
| `{$UBIQUITI_PROCESS_MAX}` | 10 | Fallback maximum process count if no specific override is defined. |
| `{$UBIQUITI_PROCESS_MAX:"dnsmasq"}` | 3 | Maximum number of allowed "dnsmasq" DNS forwarder processes. |
| `{$UBIQUITI_PROCESS_MAX:"sshd"}` | 5 | Maximum number of allowed "sshd" processes. |
| `{$UBIQUITI_PROCESS_MAX:"strongswan"}` | 5 | Maximum number of allowed "strongSwan" VPN daemon processes. |
| `{$UBIQUITI_PROCESS_MAX:"ubnt-util"}` | 2 | Maximum number of allowed "ubnt-util" system support processes. |
| `{$UBIQUITI_PROCESS_MAX:"unifi-core"}` | 3 | Maximum number of allowed "unifi-core" controller processes. |
| `{$UBIQUITI_PROCESS_MIN}` | 1 | Fallback minimum process count if no specific override is defined. |
| `{$UBIQUITI_PROCESS_MIN:"dnsmasq"}` | 1 | Minimum number of expected "dnsmasq" DNS forwarder processes. |
| `{$UBIQUITI_PROCESS_MIN:"sshd"}` | 1 | Minimum number of expected "sshd" (SSH daemon) processes. |
| `{$UBIQUITI_PROCESS_MIN:"strongswan"}` | 1 | Minimum number of expected "strongSwan" VPN daemon processes. |
| `{$UBIQUITI_PROCESS_MIN:"ubnt-util"}` | 1 | Minimum number of expected "ubnt-util" system support processes. |
| `{$UBIQUITI_PROCESS_MIN:"unifi-core"}` | 1 | Minimum number of expected "unifi-core" controller processes. |
| `{$UBIQUITI_SESSION_COUNT_MAX}` | 50000 | Higher than usual session-count could indicate DDOS attacks. Default=50k. |
| `{$UBIQUITI_STORAGE_USED_MAX:"/dev/shm"}` | 95 | Shared memory file system. Usage typically spikes with high memory applications. |
| `{$UBIQUITI_STORAGE_USED_MAX:"/opt/vyatta/config"}` | 80 | Critical system configuration storage. Should not exceed 80% usage. |
| `{$UBIQUITI_STORAGE_USED_MAX:"/root.dev"}` | 85 | Root device mount. Should stay below 85% to ensure operational integrity. |
| `{$UBIQUITI_STORAGE_USED_MAX:"/root.dev/ugw"}` | 85 | Submount of root device for UGW-specific operations. Monitor closely if used actively. |
| `{$UBIQUITI_STORAGE_USED_MAX:"/run"}` | 95 | Temporary runtime data. May fluctuate, but sustained high usage can indicate issues. |
| `{$UBIQUITI_STORAGE_USED_MAX:"/tmp"}` | 90 | Maximum allowed storage usage on /tmp mount point. Typically more flexible, but still worth monitoring. |
| `{$UBIQUITI_STORAGE_USED_MAX:"/var/log"}` | 85 | Maximum allowed storage usage on /var/log mount point before raising a warning. |
| `{$UBIQUITI_UPTIME_MIN}` | 600 | Minimum expected uptime in seconds before raising alert for recent reboot (default 600 = 10 minutes). |
| `{$UBIQUITI_VLAN_ERROR_THRESHOLD}` | 10 | Trigger threshold for interface error rates in errors per second. Applies to VLAN interfaces only. |
| `{$UBIQUITI_VPN_ERRORS_THRESHOLD}` | 10 | Trigger threshold for the number of errors (in or out) per second on ANY VPN interface. |

## Éléments surveillés (items)

| Nom | Clé | Type | Type de valeur | Unité |
|---|---|---|---|---|
| Active Sessions | `ubiquiti.sessions.active` | SNMP_AGENT | UNSIGNED |  |
| CPU Utilisation | `ubiquiti.cpu.utilisation` | SNMP_AGENT | FLOAT | % |
| Device Contact | `system.contact` | SNMP_AGENT | TEXT |  |
| Device Location | `system.location` | SNMP_AGENT | TEXT |  |
| Device Name | `system.name` | SNMP_AGENT | TEXT |  |
| Device Uptime | `system.uptime` | SNMP_AGENT | UNSIGNED | s |
| Disk Throughput Read | `ubiquiti.system.io.read` | SNMP_AGENT | UNSIGNED | Bps |
| Disk Throughput Write | `ubiquiti.system.io.write` | SNMP_AGENT | UNSIGNED | Bps |
| Memory Available | `ubiquiti.memory.available` | SNMP_AGENT | UNSIGNED | B |
| Memory Total | `ubiquiti.memory.total` | SNMP_AGENT | UNSIGNED | B |
| Swap Available | `ubiquiti.swap.available` | SNMP_AGENT | UNSIGNED | B |
| Swap Total | `ubiquiti.swap.total` | SNMP_AGENT | UNSIGNED | B |

## Règles de découverte (LLD)

| Nom | Clé | Type | Prototypes d'éléments | Prototypes de déclencheurs |
|---|---|---|---|---|
| Disk I/O | `ubiquiti.diskio.discovery` | SNMP_AGENT | 4 | 0 |
| Interfaces (Ethernet) | `ubiquiti.interface.discovery` | SNMP_AGENT | 5 | 0 |
| Interfaces (Extended) | `ubiquiti.xint.discovery` | SNMP_AGENT | 5 | 0 |
| Interfaces (VLAN) | `ubiquiti.vlan.discovery` | SNMP_AGENT | 5 | 0 |
| Memory | `ubiquiti.memory.discovery` | SNMP_AGENT | 2 | 0 |
| Processes | `ubiquiti.process.discovery` | SNMP_AGENT | 1 | 0 |
| Route Table | `ubiquiti.route.discovery` | SNMP_AGENT | 4 | 1 |
| VPN Tunnels (L2TP Based) | `ubiquiti.vpn.l2tp.discovery` | SNMP_AGENT | 5 | 0 |
| VPN Tunnels (OpenVPN Based) | `ubiquiti.vpn.openvpn.discovery` | SNMP_AGENT | 5 | 0 |
| VPN Tunnels (Policy Based) | `ubiquiti.vpn.policy.discovery` | SNMP_AGENT | 5 | 0 |
| VPN Tunnels (PPTP Based) | `ubiquiti.vpn.pptp.discovery` | SNMP_AGENT | 5 | 0 |
| VPN Tunnels (Route Based) | `ubiquiti.vpn.route.discovery` | SNMP_AGENT | 5 | 0 |
| VPN Tunnels (Teleport Based) | `ubiquiti.vpn.teleport.discovery` | SNMP_AGENT | 5 | 0 |
| VPN Tunnels (WireGuard Based) | `ubiquiti.vpn.wireguard.discovery` | SNMP_AGENT | 5 | 0 |

## Déclencheurs (triggers)

| Nom | Sévérité | Description |
|---|---|---|
| High CPU Utilisation | Moyen | CPU utilisation exceeds {$UBIQUITI_CPU_UTIL_MAX}% averaged across 5 samples. High CPU usage may impact routing performance and responsiveness. |
| High session count | Moyen | High session count detected over 5 samples. May indicate scanning, DoS, or high concurrent usage. |
| Device rebooted recently | Avertissement | The device has restarted within the last 10 minutes. This could indicate planned maintenance or unexpected reboot. |
| Route: {#ROUTEDEST}/{#ROUTEMASK} path changed | Information | Detected a stable change in routing for {#ROUTEDEST}/{#ROUTEMASK}. The outgoing interface or next hop has altered consistently over 5 polling cycles. This may indicate BGP convergence, VPN tunnel drop or fallback routing. |

## Cartographies de valeurs (value maps)

| Nom | Correspondances |
|---|---|
| interface.status.valuemap | 1→Up, 2→Down, 3→Testing, 4→Unknown, 5→Dormant, 6→Not Preent, 7→Lower Layer Down |
| route.type.valuemap | 1→Other, 2→Invalid, 3→Direct, 4→Indirect |

## Tableaux de bord (dashboards)

- Ubiquiti Firewall

---
*Documentation générée automatiquement à partir de l'export Zabbix.*
