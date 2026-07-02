# PIC Proxmox Backup Server by HTTP

## Vue d'ensemble

| Propriété | Valeur |
|---|---|
| Nom technique | `PIC Proxmox Backup Server by HTTP` |
| Nom d'affichage | PIC Proxmox Backup Server by HTTP |
| Version Zabbix | 7.4 |
| Fichier | `PIC Proxmox Backup Server by HTTP.yaml` |
| Groupes | Templates/Applications |
| Éléments (items) | 7 |
| Règles de découverte | 2 |
| Prototypes d'éléments | 15 |
| Déclencheurs | 3 |
| Macros | 6 |
| Cartographies de valeurs | 1 |
| Tableaux de bord | 0 |
| Tags | component: backup |

## Macros

| Macro | Valeur par défaut | Description |
|---|---|---|
| `{$PBS.DATASTORE.AVAILABLE.MIN}` | 10737418240 |  |
| `{$PBS.HOST}` | <HOSTNAME> |  |
| `{$PBS.PORT}` | 8007 |  |
| `{$PBS.TASKS.DAYS}` | 2 | Age of tasks to consider when looking for failed tasks (days) |
| `{$PBS.TOKEN.ID}` | USER@REALM!TOKENID | API tokens allow stateless access to most parts of the REST API by another system, software or API client. |
| `{$PBS.TOKEN.SECRET}` | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx |  |

## Éléments surveillés (items)

| Nom | Clé | Type | Type de valeur | Unité |
|---|---|---|---|---|
| PBS: API service status | `pbs.api.available` | SCRIPT | UNSIGNED |  |
| PBS: Failed tasks summary | `pbs.tasks.error.summary` | DEPENDENT | TEXT |  |
| PBS: Failed VMs | `pbs.failed.vms` | DEPENDENT | TEXT |  |
| PBS: Failed VMs Summary | `pbs.failed.vms.summary` | DEPENDENT | TEXT |  |
| PBS: Get datastore status | `pbs.datastore.status` | HTTP_AGENT | TEXT |  |
| PBS: Get disks | `pbs.disks` | HTTP_AGENT | TEXT |  |
| PBS: Get failed tasks | `pbs.tasks.error` | HTTP_AGENT | TEXT |  |

## Règles de découverte (LLD)

| Nom | Clé | Type | Prototypes d'éléments | Prototypes de déclencheurs |
|---|---|---|---|---|
| PBS: Datastore discovery | `pbs.datastore.discovery` | DEPENDENT | 7 | 0 |
| PBS: Disk discovery | `pbs.disk.discovery` | DEPENDENT | 8 | 0 |

## Déclencheurs (triggers)

| Nom | Sévérité | Description |
|---|---|---|
| PBS: API service not available | Élevé |  |
| PBS: Failed tasks found | Élevé | Failed or warning tasks occurred within the last {$PBS.TASKS.DAYS} days. |
| PBS: VM backup failures detected | Élevé | One or more VMs/CTs had backup failures within the last {$PBS.TASKS.DAYS} days. |

## Cartographies de valeurs (value maps)

| Nom | Correspondances |
|---|---|
| HTTP response status code | 200→OK, 301→Moved Permanently, 400→Bad Request, 401→Unauthorized, 403→Forbidden, 404→Not Found, 405→Method Not Allowed, 500→Internal Server Error, … (+4) |

---
*Documentation générée automatiquement à partir de l'export Zabbix.*
