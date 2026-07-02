# PIC - UPS SNMP Web Pro 3-Phase

> Template for UPS equipped with SNMP Web Pro 1.1 card — 3-phase version

## Vue d'ensemble

| Propriété | Valeur |
|---|---|
| Nom technique | `UPS SNMP Web Pro 3-Phase` |
| Nom d'affichage | PIC - UPS SNMP Web Pro 3-Phase |
| Version Zabbix | 7.4 |
| Fichier | `UPS SNMP Web Pro 3-Phase.yaml` |
| Groupes | PIC informatique - Template, Templates/Network devices |
| Éléments (items) | 31 |
| Règles de découverte | 0 |
| Prototypes d'éléments | 0 |
| Déclencheurs | 22 |
| Macros | 11 |
| Cartographies de valeurs | 0 |
| Tableaux de bord | 0 |
| Tags | class: UPS |

## Description

Template for UPS equipped with SNMP Web Pro 1.1 card — 3-phase version
Based on RFC 1628 (UPS-MIB — 1.3.6.1.2.1.33)

Tested on: SNMP Web Pro 1.1 (Pharmacie des Salines — 192.168.1.230)

Coverage:
  - Identity: manufacturer, model, firmware
  - UPS status: mode, alarms count, temperature
  - Battery: status, capacity, runtime, voltage, seconds on battery
  - Input  3-phase: voltage L1/L2/L3, frequency
  - Output 3-phase: voltage L1/L2/L3, current L1/L2/L3, load L1/L2/L3, frequency
  - Bypass 3-phase: voltage L1/L2/L3, frequency
  - EMD: temperature, humidity (if probe connected)

Security:
  - Override {$SNMP_COMMUNITY} with a Secret macro (never leave 'public')
  - Restrict SNMP polling to Zabbix Proxy IP via UPS ACL
  - Upgrade to SNMPv3 authPriv if firmware supports it

Version : 1.1.0 — Zabbix 7.4 compatible

## Macros

| Macro | Valeur par défaut | Description |
|---|---|---|
| `{$SNMP_COMMUNITY}` |  | SNMP v2c community string — MUST be changed (Secret type) |
| `{$UPS.BATTERY.CAPACITY.MIN.CRIT}` | 15 | Battery capacity critical threshold (%) |
| `{$UPS.BATTERY.CAPACITY.MIN.WARN}` | 30 | Battery capacity warning threshold (%) |
| `{$UPS.BATTERY.RUNTIME.MIN.CRIT}` | 5 | Estimated runtime critical threshold (minutes) |
| `{$UPS.BATTERY.RUNTIME.MIN.WARN}` | 10 | Estimated runtime warning threshold (minutes) |
| `{$UPS.INPUT.VOLTAGE.MAX.WARN}` | 253 | Input voltage maximum warning threshold (V) |
| `{$UPS.INPUT.VOLTAGE.MIN.WARN}` | 200 | Input voltage minimum warning threshold (V) |
| `{$UPS.OUTPUT.LOAD.MAX.CRIT}` | 95 | Output load critical threshold per phase (%) |
| `{$UPS.OUTPUT.LOAD.MAX.WARN}` | 80 | Output load warning threshold per phase (%) |
| `{$UPS.TEMP.MAX.CRIT}` | 50 | UPS temperature critical threshold (°C) |
| `{$UPS.TEMP.MAX.WARN}` | 40 | UPS temperature warning threshold (°C) |

## Éléments surveillés (items)

| Nom | Clé | Type | Type de valeur | Unité |
|---|---|---|---|---|
| Battery: Capacity | `ups.battery.charge` | SNMP_AGENT | UNSIGNED | % |
| Battery: Estimated Runtime | `ups.battery.runtime` | SNMP_AGENT | UNSIGNED | min |
| Battery: Seconds on Battery | `ups.battery.seconds.on.battery` | SNMP_AGENT | UNSIGNED | s |
| Battery: Status | `ups.battery.status` | SNMP_AGENT | UNSIGNED |  |
| Battery: Voltage (Positive string) | `ups.battery.voltage` | SNMP_AGENT | FLOAT | V |
| Bypass: Frequency | `ups.bypass.frequency` | SNMP_AGENT | FLOAT | Hz |
| Bypass: Voltage L1 | `ups.bypass.voltage.l1` | SNMP_AGENT | FLOAT | V |
| Bypass: Voltage L2 | `ups.bypass.voltage.l2` | SNMP_AGENT | FLOAT | V |
| Bypass: Voltage L3 | `ups.bypass.voltage.l3` | SNMP_AGENT | FLOAT | V |
| EMD: Humidity | `ups.emd.humidity` | SNMP_AGENT | FLOAT | % |
| EMD: Temperature | `ups.emd.temperature` | SNMP_AGENT | FLOAT | °C |
| Input: Frequency | `ups.input.frequency` | SNMP_AGENT | FLOAT | Hz |
| Input: Voltage L1 | `ups.input.voltage.l1` | SNMP_AGENT | FLOAT | V |
| Input: Voltage L2 | `ups.input.voltage.l2` | SNMP_AGENT | FLOAT | V |
| Input: Voltage L3 | `ups.input.voltage.l3` | SNMP_AGENT | FLOAT | V |
| Output: Current L1 | `ups.output.current.l1` | SNMP_AGENT | FLOAT | A |
| Output: Current L2 | `ups.output.current.l2` | SNMP_AGENT | FLOAT | A |
| Output: Current L3 | `ups.output.current.l3` | SNMP_AGENT | FLOAT | A |
| Output: Frequency | `ups.output.frequency` | SNMP_AGENT | FLOAT | Hz |
| Output: Load Level L1 | `ups.output.load.l1` | SNMP_AGENT | UNSIGNED | % |
| Output: Load Level L2 | `ups.output.load.l2` | SNMP_AGENT | UNSIGNED | % |
| Output: Load Level L3 | `ups.output.load.l3` | SNMP_AGENT | UNSIGNED | % |
| Output: Voltage L1 | `ups.output.voltage.l1` | SNMP_AGENT | FLOAT | V |
| Output: Voltage L2 | `ups.output.voltage.l2` | SNMP_AGENT | FLOAT | V |
| Output: Voltage L3 | `ups.output.voltage.l3` | SNMP_AGENT | FLOAT | V |
| UPS: Active Alarms Count | `ups.alarms.present` | SNMP_AGENT | UNSIGNED |  |
| UPS: Agent Firmware Version | `ups.ident.agent.version` | SNMP_AGENT | CHAR |  |
| UPS: Manufacturer | `ups.ident.manufacturer` | SNMP_AGENT | CHAR |  |
| UPS: Model | `ups.ident.model` | SNMP_AGENT | CHAR |  |
| UPS: Output Source (Mode) | `ups.output.source` | SNMP_AGENT | UNSIGNED |  |
| UPS: Temperature | `ups.temperature` | SNMP_AGENT | FLOAT | °C |

## Déclencheurs (triggers)

| Nom | Sévérité | Description |
|---|---|---|
| Battery: Capacity critical (<{$UPS.BATTERY.CAPACITY.MIN.CRIT}%) | Désastre |  |
| Battery: DEPLETED — imminent shutdown | Désastre | upsBatteryStatus = 4 (Depleted). UPS shutdown imminent. |
| Battery: Capacity warning (<{$UPS.BATTERY.CAPACITY.MIN.WARN}%) | Élevé |  |
| Battery: Low battery warning | Élevé | upsBatteryStatus = 3 (Low). Imminent shutdown if power not restored. |
| Battery: Runtime critical (<{$UPS.BATTERY.RUNTIME.MIN.CRIT} min) | Élevé |  |
| Battery: Status unknown | Élevé | upsBatteryStatus = 1 (Unknown). |
| Output L1: Load critical (>{$UPS.OUTPUT.LOAD.MAX.CRIT}%) | Élevé |  |
| Output L2: Load critical (>{$UPS.OUTPUT.LOAD.MAX.CRIT}%) | Élevé |  |
| Output L3: Load critical (>{$UPS.OUTPUT.LOAD.MAX.CRIT}%) | Élevé |  |
| UPS: In bypass mode — no protection | Élevé | upsOutputSource = 4 (Bypass). No conditioning, no battery backup. |
| UPS: No SNMP data for 2 minutes | Élevé | SNMP polling failed — check community string and ACL. |
| UPS: Running on battery (power failure) | Élevé | upsOutputSource = 5 (Battery). Grid power failure detected. |
| UPS: Temperature critical (>{$UPS.TEMP.MAX.CRIT}) | Élevé |  |
| UPS: {ITEM.LASTVALUE} active alarm(s) — check web interface | Élevé | upsAlarmsPresent > 0. Connect to UPS web interface to identify alarms. Current known warning: Battery replace. |
| Battery: Runtime warning (<{$UPS.BATTERY.RUNTIME.MIN.WARN} min) | Avertissement |  |
| Input L1: Voltage out of range [{$UPS.INPUT.VOLTAGE.MIN.WARN}-{$UPS.INPUT.VOLTAGE.MAX.WARN}V] | Avertissement |  |
| Input L2: Voltage out of range [{$UPS.INPUT.VOLTAGE.MIN.WARN}-{$UPS.INPUT.VOLTAGE.MAX.WARN}V] | Avertissement |  |
| Input L3: Voltage out of range [{$UPS.INPUT.VOLTAGE.MIN.WARN}-{$UPS.INPUT.VOLTAGE.MAX.WARN}V] | Avertissement |  |
| Output L1: Load warning (>{$UPS.OUTPUT.LOAD.MAX.WARN}%) | Avertissement |  |
| Output L2: Load warning (>{$UPS.OUTPUT.LOAD.MAX.WARN}%) | Avertissement |  |
| Output L3: Load warning (>{$UPS.OUTPUT.LOAD.MAX.WARN}%) | Avertissement |  |
| UPS: Temperature warning (>{$UPS.TEMP.MAX.WARN}) | Avertissement |  |

---
*Documentation générée automatiquement à partir de l'export Zabbix.*
