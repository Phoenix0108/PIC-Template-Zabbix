# PIC - FTTO template

> Template ICMP Ping for FTTO

## Vue d'ensemble

| Propriété | Valeur |
|---|---|
| Nom technique | `PIC - FTTO template` |
| Nom d'affichage | PIC - FTTO template |
| Version Zabbix | 7.4 |
| Fichier | `Template ICMP Ping for FTTO.yaml` |
| Groupes | PIC informatique - Template |
| Éléments (items) | 3 |
| Règles de découverte | 0 |
| Prototypes d'éléments | 0 |
| Déclencheurs | 3 |
| Macros | 2 |
| Cartographies de valeurs | 1 |
| Tableaux de bord | 0 |
| Tags | class: network, target: icmp |

## Description

Template ICMP Ping for FTTO

## Installation & configuration

## Setup

Set `Threshold of ICMP packet loss` and `Threshold of the average ICMP response`

## Macros

| Macro | Valeur par défaut | Description |
|---|---|---|
| `{$ICMP_LOSS_WARN}` | 20 | Warning threshold of ICMP packet loss in %. |
| `{$ICMP_RESPONSE_TIME_WARN}` | 0.15 | Warning threshold of the average ICMP response time in seconds. |

## Éléments surveillés (items)

| Nom | Clé | Type | Type de valeur | Unité |
|---|---|---|---|---|
| ICMP loss | `icmppingloss` | SIMPLE | FLOAT | % |
| ICMP ping | `icmpping` | SIMPLE | UNSIGNED |  |
| ICMP response time | `icmppingsec` | SIMPLE | FLOAT | s |

## Déclencheurs (triggers)

| Nom | Sévérité | Description |
|---|---|---|
| ICMP Ping: Unavailable by ICMP ping | Désastre | Le lien Internet FTTH ne répond plus aux requêtes ICMP depuis 3 minutes. |
| ICMP Ping: High ICMP ping loss | Avertissement | Loss: {ITEM.LASTVALUE1} |
| ICMP Ping: High ICMP ping response time | Avertissement | Average ICMP response time is too high. |

## Cartographies de valeurs (value maps)

| Nom | Correspondances |
|---|---|
| Service state | 0→Down, 1→Up |

---
*Documentation générée automatiquement à partir de l'export Zabbix.*
