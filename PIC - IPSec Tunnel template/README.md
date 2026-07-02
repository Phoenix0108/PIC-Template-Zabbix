# PIC - IPSec Tunnel template

> Template ICMP Ping for IPSec Tunnel monitoring between two sites (e.g. Bastia <-> Ajaccio)

## Vue d'ensemble

| Propriété | Valeur |
|---|---|
| Nom technique | `PIC - IPSec Tunnel template` |
| Nom d'affichage | PIC - IPSec Tunnel template |
| Version Zabbix | 7.4 |
| Fichier | `PIC - IPSec Tunnel template.yaml` |
| Groupes | PIC informatique - Template |
| Éléments (items) | 3 |
| Règles de découverte | 0 |
| Prototypes d'éléments | 0 |
| Déclencheurs | 3 |
| Macros | 3 |
| Cartographies de valeurs | 1 |
| Tableaux de bord | 0 |
| Tags | class: network, target: icmp, target: tunnel |

## Description

Template ICMP Ping for IPSec Tunnel monitoring between two sites (e.g. Bastia <-> Ajaccio)

## Installation & configuration

## Setup

Ce template surveille la qualité du tunnel IPSec entre deux sites via des pings ICMP entre les proxys.

### Configuration
1. Créer un hôte représentant le tunnel (ex : "Tunnel IPSec Bastia-Ajaccio")
2. Définir l'interface de l'hôte avec l'IP du proxy distant (ex : proxy Ajaccio)
3. Définir "Monitored by" sur le proxy local (ex : proxy Bastia)
4. Appliquer ce template sur cet hôte

### Macros à configurer
- `{$ICMP_COUNT}` : Nombre de paquets ICMP envoyés (défaut : 20)
- `{$ICMP_LOSS_WARN}` : Seuil de perte de paquets en % (défaut : 20)
- `{$ICMP_RESPONSE_TIME_WARN}` : Seuil de latence moyenne en secondes (défaut : 0.15)

## Macros

| Macro | Valeur par défaut | Description |
|---|---|---|
| `{$ICMP_COUNT}` | 20 | Nombre de paquets ICMP envoyés par test. |
| `{$ICMP_LOSS_WARN}` | 20 | Seuil d'alerte de perte de paquets ICMP en %. |
| `{$ICMP_RESPONSE_TIME_WARN}` | 0.15 | Seuil HIGH de latence ICMP moyenne en secondes (ex: 0.15 = 150ms). |

## Éléments surveillés (items)

| Nom | Clé | Type | Type de valeur | Unité |
|---|---|---|---|---|
| ICMP loss | `icmppingloss[,{$ICMP_COUNT}]` | SIMPLE | FLOAT | % |
| ICMP ping | `icmpping[,{$ICMP_COUNT}]` | SIMPLE | UNSIGNED |  |
| ICMP response time | `icmppingsec[,{$ICMP_COUNT}]` | SIMPLE | FLOAT | s |

## Déclencheurs (triggers)

| Nom | Sévérité | Description |
|---|---|---|
| IPSec Tunnel: Unavailable by ICMP ping | Désastre | Le tunnel IPSec ne répond plus aux requêtes ICMP depuis 3 tentatives consécutives. |
| IPSec Tunnel: High ICMP ping response time | Élevé | La latence moyenne du tunnel IPSec est trop élevée (>{$ICMP_RESPONSE_TIME_WARN}s sur 5 minutes). |
| IPSec Tunnel: High ICMP ping loss | Avertissement | Le tunnel IPSec présente une perte de paquets ICMP supérieure à {$ICMP_LOSS_WARN}%. |

## Cartographies de valeurs (value maps)

| Nom | Correspondances |
|---|---|
| Service state | 0→Down, 1→Up |

---
*Documentation générée automatiquement à partir de l'export Zabbix.*
