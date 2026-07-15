# PIC - Dell Compellent SC by SNMP

> Template de monitoring pour baies Dell Compellent / Storage Center SC Series (ex : SCv3020)

## Vue d'ensemble

| Propriété | Valeur |
|---|---|
| Nom technique | `PIC - Dell Compellent SC by SNMP` |
| Nom d'affichage | PIC - Dell Compellent SC by SNMP |
| Version Zabbix | 7.4 |
| Fichier | `PIC - Dell Compellent SC by SNMP.yaml` |
| Groupes | PIC informatique - Template, Templates/Storage |
| Éléments (items) | 9 |
| Règles de découverte | 7 |
| Déclencheurs | 2 (+ prototypes) |
| Macros | 3 |
| Cartographies de valeurs | 3 |
| Tags | class: storage, target: SAN, vendor: Dell |

## Description

Template SNMP v2c pour les baies **Dell Compellent / Storage Center SC Series** (testé pour le modèle **SCv3020**).
Il s'appuie sur la **COMPELLENT-MIB** (entreprise `1.3.6.1.4.1.16139`).

- Statut global de la baie, version, numéro de série (Service Tag)
- Découverte des contrôleurs, disques, enceintes, capteurs de température, ventilateurs, alimentations et volumes
- Détection des composants `down` / `degraded` (type `ScStatus`) et de la santé disque

> **Note OID** : Dell migre progressivement de la branche historique `1.3.6.1.4.1.16139` vers
> `1.3.6.1.4.1.674.11000.2000.500.1`. Ce template utilise la branche `16139`, prise en charge par
> Storage Center OS sur SCv3020. Adapter le préfixe si votre firmware n'expose que la nouvelle branche.

Protocole : SNMP v2c
Auteur     : PIC informatique
Version    : 1.0 — Zabbix 7.4

## Prérequis

1. Activer SNMP sur la baie Storage Center (Dell Storage Manager → System → SNMP).
2. Renseigner la communauté SNMP en lecture dans la macro `{$SNMP_COMMUNITY}`.
3. Associer une interface **SNMP** sur l'hôte Zabbix pointant vers l'IP de gestion de la baie.

## Macros

| Macro | Valeur par défaut | Description |
|---|---|---|
| `{$SNMP_COMMUNITY}` | picinformatique | Communauté SNMP v2c |
| `{$TEMP_CRIT}` | 45 | Température critique (°C) |
| `{$TEMP_WARN}` | 38 | Température d'alerte (°C) |

## Éléments surveillés (items)

| Nom | OID |
|---|---|
| ICMP Ping / Loss / Response Time | — |
| Product Name | `.1.3.6.1.4.1.16139.2.1.0` |
| Product Description | `.1.3.6.1.4.1.16139.2.2.0` |
| Version | `.1.3.6.1.4.1.16139.2.4.0` |
| Serial Number / Service Tag | `.1.3.6.1.4.1.16139.2.5.0` |
| Build Number | `.1.3.6.1.4.1.16139.2.7.0` |
| Global Status | `.1.3.6.1.4.1.16139.2.6.0` |

## Règles de découverte (LLD)

| Nom | OID de découverte | Prototypes |
|---|---|---|
| Controllers | `1.3.6.1.4.1.16139.2.13.1.4` | Status, Model |
| Physical Disks | `1.3.6.1.4.1.16139.2.14.1.4` | Status, Healthy, Size |
| Enclosures | `1.3.6.1.4.1.16139.2.15.1.4` | Status |
| Temperature Sensors | `1.3.6.1.4.1.16139.2.23.1.4` | Value, Status |
| Fans | `1.3.6.1.4.1.16139.2.20.1.4` | Status, Speed |
| Power Supplies | `1.3.6.1.4.1.16139.2.21.1.4` | Status |
| Volumes | `1.3.6.1.4.1.16139.2.26.1.4` | Status |

> La taille disque est exposée en Go par la MIB et convertie en octets (× 1 073 741 824).

## Déclencheurs (triggers)

| Nom | Sévérité |
|---|---|
| Controller {#CTLR} is DOWN | Désastre |
| Global status is CRITICAL | Élevé |
| Controller {#CTLR} is DEGRADED | Élevé |
| Disk {#DISK} is DOWN | Élevé |
| Enclosure {#ENCL} is DOWN | Élevé |
| Power Supply {#PSU} problem | Élevé |
| Temperature {#SENSOR} is CRITICAL | Élevé |
| Volume {#VOLUME} is DOWN | Élevé |
| Global status is non-critical | Moyen |
| Disk {#DISK} is DEGRADED / not healthy | Moyen |
| Enclosure {#ENCL} is DEGRADED | Moyen |
| Fan {#FAN} problem | Moyen |
| Volume {#VOLUME} is DEGRADED | Moyen |
| Temperature {#SENSOR} is HIGH | Avertissement |
| {HOST.NAME} is unreachable | Élevé |
| Packet loss HIGH | Avertissement |

## Cartographies de valeurs (value maps)

| Nom | Correspondances |
|---|---|
| Compellent ScStatus | 1→up, 2→down, 3→degraded |
| Compellent Global Status | 1→other, 2→unknown, 3→ok, 4→noncritical, 5→critical, 6→nonrecoverable |
| Compellent TruthValue | 1→true, 2→false |

---
*Documentation générée à partir de l'export Zabbix. OID issus de la COMPELLENT-MIB (Dell Compellent, entreprise 16139).*
