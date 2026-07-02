# PIC - Serveur AD by Zabbix agent

> Template 100% native (agent Zabbix seul, aucun script ni UserParameter a déployer) pour serveurs Windows assurant des rôles AD DS / DNS / DHCP / RDS / fichiers.

## Vue d'ensemble

| Propriété | Valeur |
|---|---|
| Nom technique | `PIC - Serveur AD by Zabbix agent` |
| Nom d'affichage | PIC - Serveur AD by Zabbix agent |
| Version Zabbix | 7.4 |
| Fichier | `PIC - Serveur AD by Zabbix agent.yaml` |
| Groupes | Templates/Operating systems |
| Éléments (items) | 19 |
| Règles de découverte | 2 |
| Prototypes d'éléments | 2 |
| Déclencheurs | 13 |
| Macros | 10 |
| Cartographies de valeurs | 0 |
| Tableaux de bord | 0 |
| Tags | class: ADDS, target: windows server |

## Description

Template 100% native (agent Zabbix seul, aucun script ni UserParameter a déployer) pour serveurs Windows assurant des rôles AD DS / DNS / DHCP / RDS / fichiers.
A LIER EN PLUS de la template Windows de base au niveau de l'hôte.

COLLECTE : uniquement des clés agent intégrées (service.discovery, wmi.getall, perf_counter_en, eventlog, net.dns, net.tcp.service). Import + liaison, aucune macro obligatoire : le domaine AD est auto-découvert via WMI. Rien a installer sur le serveur.

SECURITE BY DESIGN : aucun powershell.exe spawné, aucun repertoire de scripts inscriptible (surface d'elevation locale supprimee), agent stock. Lecture seule via le compte machine (LocalSystem). system.run reste inutile et desactive.

REPLICATION (catch direct du scenario de desynchro) : signaux natifs via journal Directory Service (2042 = TSL depasse -> DISASTER ; 1864 = >24h -> alerte precoce ; 1311/1865/1925/1988 = KCC/lingering) + jauge DRA Pending. Pas de lecture live du Tombstone Lifetime ni de retard en secondes (necessitaient PowerShell) — voir la doc pour les ecarts assumes (occupation d'etendue DHCP, autorisation/failover DHCP).

Version : 2.0.0 (native) — Zabbix 7.4

## Macros

| Macro | Valeur par défaut | Description |
|---|---|---|
| `{$AD.DNS.EXT.PROBE}` | www.microsoft.com | Nom public utilisé pour tester la récursion DNS sortante. |
| `{$AD.LDAP.BIND.MS.WARN}` | 50 | Latence de bind LDAP (ms, moyenne 15 min) avant alerte. |
| `{$AD.LOCKOUT.WARN}` | 5 | Verrouillages 4740 sur 10 min avant alerte. |
| `{$AD.REPL.DRA.PENDING.WARN}` | 10 | Backlog DRA soutenu (min sur 30 min) avant alerte. |
| `{$AD.SERVICE.MATCHES}` | ^(NTDS\|Netlogon\|Kdc\|IsmServ\|DFSR\|Dfs\|ADWS\|W32Time\|DNS\|DHCPServer\|TermService\|SessionEnv\|UmRdpService\|TScPubRPC\|RDMS\|Tssdis\|TSGateway\|TermServLicensing\|LanmanServer\|srmsvc\|MSDTC)$ | Regex des noms courts de services de rôle supervisés. |
| `{$AD.SERVICE.NOT_MATCHES}` | ^$ | Regex d'exclusion (par défaut n'exclut rien). |
| `{$DHCP.NACKS.WARN}` | 1 | NAK/sec (moyenne 15 min) avant alerte. |
| `{$DNS.RECURSION.FAIL.WARN}` | 5 | Échecs de récursion/sec (moyenne 15 min) avant alerte. |
| `{$W32TIME.OFFSET.RAW.DISASTER}` | 300000000 | Seuil HIGH décalage horloge (hypothèse µs => 300s = tolérance Kerberos). A CALIBRER. |
| `{$W32TIME.OFFSET.RAW.WARN}` | 30000000 | Seuil AVERAGE décalage horloge (valeur BRUTE, hypothèse µs => 30s). A CALIBRER. |

## Éléments surveillés (items)

| Nom | Clé | Type | Type de valeur | Unité |
|---|---|---|---|---|
| AD DS : journal — DFS Replication SYSVOL (4012/5002/5008/2213) | `eventlog[DFS Replication,,,,"^(4012\|5002\|5008\|2213)$",,skip]` | ZABBIX_PASSIVE | LOG |  |
| AD DS : journal — erreurs réplication/KCC/lingering (1311/1865/1925/1988) | `eventlog[Directory Service,,,,"^(1311\|1865\|1925\|1988)$",,skip]` | ZABBIX_PASSIVE | LOG |  |
| AD DS : journal — réplication tardive >24h (1864) | `eventlog[Directory Service,,,,"^1864$",,skip]` | ZABBIX_PASSIVE | LOG |  |
| AD DS : journal — Tombstone Lifetime dépassé (2042) | `eventlog[Directory Service,,,,"^2042$",,skip]` | ZABBIX_PASSIVE | LOG |  |
| AD DS : journal — verrouillages de compte (4740) | `eventlog[Security,,,,"^4740$",,skip]` | ZABBIX_PASSIVE | LOG |  |
| AD DS : port LDAP (389) répond | `net.tcp.service[ldap]` | ZABBIX_PASSIVE | UNSIGNED |  |
| AD DS : rôle du domaine (Win32 DomainRole) | `wmi.get[root\cimv2,"select DomainRole from Win32_ComputerSystem"]` | ZABBIX_PASSIVE | UNSIGNED |  |
| AD DS : synchros de réplication en attente (DRA) | `perf_counter_en["\DirectoryServices(NTDS)\DRA Pending Replication Synchronizations"]` | ZABBIX_PASSIVE | UNSIGNED |  |
| AD DS : temps de bind LDAP | `perf_counter_en["\DirectoryServices(NTDS)\LDAP Bind Time"]` | ZABBIX_PASSIVE | UNSIGNED | ms |
| DHCP : longueur de file active | `perf_counter_en["\DHCP Server\Active Queue Length"]` | ZABBIX_PASSIVE | UNSIGNED |  |
| DHCP : Nacks/sec | `perf_counter_en["\DHCP Server\Nacks/sec"]` | ZABBIX_PASSIVE | FLOAT |  |
| DNS : journal serveur — erreurs | `eventlog[DNS Server,,Error,,,,skip]` | ZABBIX_PASSIVE | LOG |  |
| DNS : résolution externe (récursion) | `net.dns[,{$AD.DNS.EXT.PROBE},A,5,2]` | ZABBIX_PASSIVE | UNSIGNED |  |
| DNS : échecs de récursion/sec | `perf_counter_en["\DNS\Recursive Query Failure/sec"]` | ZABBIX_PASSIVE | FLOAT |  |
| Fichiers : fichiers ouverts | `perf_counter_en["\Server\Files Open"]` | ZABBIX_PASSIVE | UNSIGNED |  |
| Fichiers : sessions SMB | `perf_counter_en["\Server\Server Sessions"]` | ZABBIX_PASSIVE | UNSIGNED |  |
| RDS : sessions actives | `perf_counter_en["\Terminal Services\Active Sessions"]` | ZABBIX_PASSIVE | UNSIGNED |  |
| RDS : sessions totales | `perf_counter_en["\Terminal Services\Total Sessions"]` | ZABBIX_PASSIVE | UNSIGNED |  |
| Temps : décalage d'horloge calculé (W32Time) | `perf_counter_en["\Windows Time Service\Computed Time Offset"]` | ZABBIX_PASSIVE | FLOAT |  |

## Règles de découverte (LLD)

| Nom | Clé | Type | Prototypes d'éléments | Prototypes de déclencheurs |
|---|---|---|---|---|
| Découverte du domaine AD (auto) | `wmi.getall[root\cimv2,"select Domain from Win32_ComputerSystem"]` | ZABBIX_PASSIVE | 1 | 0 |
| Services de rôle Windows (découverte) | `service.discovery` | ZABBIX_PASSIVE | 1 | 0 |

## Déclencheurs (triggers)

| Nom | Sévérité | Description |
|---|---|---|
| AD DS : Tombstone Lifetime dépassé sur {HOST.NAME} (événement 2042) | Désastre | Réplication absente depuis plus que le Tombstone Lifetime. NE PAS laisser rattraper : isoler, dater la dernière réplic par partenaire, nettoyage repadmin /removelingeringobjects OU démotion forcée + rebuild. Activer Strict Replication Consistency forêt-wide. |
| AD DS : erreurs de réplication/KCC/lingering sur {HOST.NAME} | Élevé |  |
| Temps : décalage d'horloge critique (seuil Kerberos) sur {HOST.NAME} | Élevé | Décalage au-delà de la tolérance Kerberos. Vérifier la hiérarchie de temps, la source NTP du PDC emulator, qu'aucun DC ne synchronise sur Local CMOS Clock. |
| AD DS : anomalie de réplication SYSVOL (DFSR) sur {HOST.NAME} | Moyen |  |
| AD DS : backlog de réplication soutenu sur {HOST.NAME} | Moyen | Synchros entrantes en attente de façon persistante. Vérifier repadmin /showrepl et la connectivité inter-DC. |
| AD DS : DC non répliqué depuis >24h sur {HOST.NAME} (événement 1864) | Moyen | Alerte précoce : un ou plusieurs DC n'ont pas répliqué dans les fenêtres surveillées par le KCC. |
| AD DS : pic de verrouillages de compte sur {HOST.NAME} | Moyen | Volume anormal de verrouillages 4740. Corréler 4625/4771 côté SIEM (brute force / password spray). |
| DHCP : NAK soutenus sur {HOST.NAME} | Moyen | Conflits d'adressage / mauvaise config d'étendue. |
| DNS : erreurs dans le journal serveur sur {HOST.NAME} | Moyen |  |
| DNS : résolution externe en échec sur {HOST.NAME} | Moyen | Récursion/forwarders en panne. Vérifier forwarders, root hints, sortie 53. |
| Temps : décalage d'horloge élevé sur {HOST.NAME} | Moyen | Dérive d'horloge. Approcher la tolérance Kerberos (MaxTolerance, 300s) casse l'authentification et fausse l'horodatage des logs (corrélation SIEM/forensic). |
| AD DS : latence de bind LDAP élevée sur {HOST.NAME} | Avertissement |  |
| DNS : échecs de récursion soutenus sur {HOST.NAME} | Avertissement |  |

---
*Documentation générée automatiquement à partir de l'export Zabbix.*
