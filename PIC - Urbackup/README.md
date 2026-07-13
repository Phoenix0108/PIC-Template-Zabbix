# PIC - Urbackup

> ## Template UrBackup — HTTP Agent pur, sans script externe

## Vue d'ensemble

| Propriété | Valeur |
|---|---|
| Nom technique | `PIC - Urbackup` |
| Nom d'affichage | PIC - Urbackup |
| Version Zabbix | 7.4 |
| Fichier | `PIC - Urbackup.yaml` |
| Groupes | PIC informatique - Template, Templates/Applications |
| Éléments (items) | 13 |
| Règles de découverte | 2 |
| Prototypes d'éléments | 15 |
| Déclencheurs | 13 |
| Macros | 17 |
| Cartographies de valeurs | 0 |
| Tableaux de bord | 1 |
| Tags | class: save, target: urbackup |

## Description

## Template UrBackup — HTTP Agent pur, sans script externe
Collecte via l'API HTTP UrBackup :55414 entièrement depuis Zabbix.
L'authentification challenge-response (PBKDF2-HMAC-SHA256 ou MD5 legacy) est gérée
par un item SCRIPT JavaScript embarqué (Duktape ES5).

### Architecture
- Un item maître SCRIPT collecte toutes les données en une seule exécution (toutes les 5 min)
- Tous les autres items sont DEPENDENT et extraient leurs valeurs via JSONPath
- Deux règles LLD : découverte des clients serveurs/Linux + utilisation disque

### Supervision
- Statut en ligne/hors ligne de tous les clients
- Échecs backup fichiers (distingués serveurs/Linux vs postes de travail)
- Échecs backup images
- Âge des derniers backups (fichier + image) par client serveur/Linux
- Utilisation disque par client serveur/Linux
- Backups en cours

### Prérequis
- Zabbix 7.4+ (item SCRIPT avec timeout configurable par item)
- UrBackup Server 2.4+ avec API HTTP active (port 55414)
- Réseau : Zabbix Server/Proxy doit joindre {$URBACKUP_SERVER}:{$URBACKUP_PORT}

### Sécurité
- {$URBACKUP_PASS} déclaré en SECRET_TEXT → masqué dans l'UI Zabbix
- Utilisateur UrBackup dédié lecture seule recommandé
- Aucun fichier de credential sur le système de fichiers

## Macros

| Macro | Valeur par défaut | Description |
|---|---|---|
| `{$URBACKUP_BACKUP_AGE_HIGH}` | 172800 | Seuil HIGH âge dernier backup fichier en secondes (défaut : 48h) |
| `{$URBACKUP_BACKUP_AGE_HIGH_LABEL}` | 48h | Libellé lisible du seuil HIGH fichier — affiché dans les noms de triggers |
| `{$URBACKUP_BACKUP_AGE_WARN}` | 86400 | Seuil WARNING âge dernier backup fichier en secondes (défaut : 24h) |
| `{$URBACKUP_BACKUP_AGE_WARN_LABEL}` | 24h | Libellé lisible du seuil WARNING fichier — affiché dans les noms de triggers |
| `{$URBACKUP_IMAGE_AGE_HIGH}` | 1209600 | Seuil HIGH âge dernier backup image en secondes (défaut : 14 jours) |
| `{$URBACKUP_IMAGE_AGE_HIGH_LABEL}` | 14 jours | Libellé lisible du seuil HIGH image — affiché dans les noms de triggers |
| `{$URBACKUP_IMAGE_AGE_WARN}` | 604800 | Seuil WARNING âge dernier backup image en secondes (défaut : 7 jours) |
| `{$URBACKUP_IMAGE_AGE_WARN_LABEL}` | 7 jours | Libellé lisible du seuil WARNING image — affiché dans les noms de triggers |
| `{$URBACKUP_NO_DATA_TIMEOUT}` | 15m | Délai sans données avant déclenchement du trigger API indisponible |
| `{$URBACKUP_PASS}` |  | Mot de passe du compte UrBackup — stocké en SECRET_TEXT, jamais visible dans lUI Zabbix |
| `{$URBACKUP_PORT}` | 55414 | Port de lAPI HTTP UrBackup (défaut : 55414) |
| `{$URBACKUP_SERVER}` | 127.0.0.1 | IP ou hostname du serveur UrBackup — à surcharger sur lhôte Zabbix |
| `{$URBACKUP_SERVER_NAMES}` | .^ | Regex sur le nom client pour forcer lInclusion dans la supervision serveur. Exemple : ^(SERAG\|SRVADALGE)$ pour inclure ces deux clients nommément. Défaut .^ = expression qui ne correspond à rien = fonctionnalité inactive. Utile pour les vieux agents qui ne remontent pas os_version_string. |
| `{$URBACKUP_STUCK_FREEZE}` | 1h | Durée pendant laquelle la progression du backup doit être figée pour déclencher les alertes "sauvegarde bloquée" (suffixe de temps Zabbix). Défaut : 1h. Augmenter à 2h ou 3h si des backups très lents génèrent des faux positifs. |
| `{$URBACKUP_STUCK_HIGH_DAYS}` | 5d | Durée continue de backup en cours avant alerte HIGH "sauvegarde bloquée" (suffixe de temps Zabbix). Combiné avec la progression figée depuis {$URBACKUP_STUCK_FREEZE}. Défaut : 5d. Au-delà, intervention manuelle requise. |
| `{$URBACKUP_STUCK_WARN_DAYS}` | 3d | Durée continue de backup en cours avant alerte AVERAGE "sauvegarde possiblement bloquée" (suffixe de temps Zabbix). Combiné avec la progression figée depuis {$URBACKUP_STUCK_FREEZE}. Défaut : 3d. Si le trigger HIGH se déclenche ({$URBACKUP_STUCK_HIGH_DAYS}), intervenir. |
| `{$URBACKUP_USER}` | zabbix_monitor | Compte UrBackup dédié au monitoring — droits lecture seule recommandés |

## Éléments surveillés (items)

| Nom | Clé | Type | Type de valeur | Unité |
|---|---|---|---|---|
| UrBackup: Backups en cours | `urbackup.backups.running` | DEPENDENT | UNSIGNED |  |
| UrBackup: Clients configurés (total) | `urbackup.clients.total` | DEPENDENT | UNSIGNED |  |
| UrBackup: Clients en ligne | `urbackup.clients.online` | DEPENDENT | UNSIGNED |  |
| UrBackup: Clients hors ligne | `urbackup.clients.offline` | DEPENDENT | UNSIGNED |  |
| UrBackup: Données brutes (master) | `urbackup.http.master` | SCRIPT | TEXT |  |
| UrBackup: Liste postes de travail avec backup fichier en échec | `urbackup.workstation.file.failures.list` | DEPENDENT | TEXT |  |
| UrBackup: Liste serveurs/Linux avec backup fichier en échec | `urbackup.server.file.failures.list` | DEPENDENT | TEXT |  |
| UrBackup: Liste serveurs/Linux avec backup image en échec | `urbackup.image.backup.failures.list` | DEPENDENT | TEXT |  |
| UrBackup: Liste serveurs/Linux hors ligne avec backup fichier en échec | `urbackup.server.file.failures.offline.list` | DEPENDENT | TEXT |  |
| UrBackup: Échecs backup fichiers — postes de travail | `urbackup.workstation.file.failures` | DEPENDENT | UNSIGNED |  |
| UrBackup: Échecs backup fichiers — serveurs/Linux | `urbackup.server.file.failures` | DEPENDENT | UNSIGNED |  |
| UrBackup: Échecs backup fichiers — serveurs/Linux hors ligne | `urbackup.server.file.failures.offline` | DEPENDENT | UNSIGNED |  |
| UrBackup: Échecs backup images (global) | `urbackup.image.backup.failures` | DEPENDENT | UNSIGNED |  |

## Règles de découverte (LLD)

| Nom | Clé | Type | Prototypes d'éléments | Prototypes de déclencheurs |
|---|---|---|---|---|
| UrBackup: Découverte des clients | `urbackup.clients.discovery` | DEPENDENT | 12 | 8 |
| UrBackup: Découverte utilisation disque | `urbackup.disk.discovery` | DEPENDENT | 3 | 0 |

## Déclencheurs (triggers)

| Nom | Sévérité | Description |
|---|---|---|
| UrBackup: [{#CLIENT_NAME}] Pas de backup fichier depuis >{$URBACKUP_BACKUP_AGE_HIGH_LABEL} | Élevé | Aucun backup fichier depuis plus de {$URBACKUP_BACKUP_AGE_HIGH_LABEL} pour {#CLIENT_NAME} (IP: {#CLIENT_IP}). Aucun backup en cours et le client est EN LIGNE — la situation est anormale et critique. Si la machine est éteinte/hors ligne, l'alerte bascule en AVERAGE (trigger "(client hors ligne)"), ce qui résout cette alerte HIGH et ferme le ticket GLPI associé. Seuil HIGH = {$URBACKUP_BACKUP_AGE_HIGH} secondes ({$URBACKUP_BACKUP_AGE_HIGH_LABEL}). Actions urgentes : - Vérifier que le client est en ligne (voir trigger "hors ligne") - Vérifier le service UrBackup Client sur {#CLIENT_NAME} - Consulter les logs UrBackup pour identifier la cause de l'interruption - Lancer manuellement un backup depuis l'interface UrBackup - Vérifier qu'aucune exclusion de planification n'est active sur ce client |
| UrBackup: [{#CLIENT_NAME}] Pas de backup image depuis >{$URBACKUP_IMAGE_AGE_HIGH_LABEL} | Élevé | Aucun backup image depuis plus de {$URBACKUP_IMAGE_AGE_HIGH_LABEL} pour {#CLIENT_NAME} (IP: {#CLIENT_IP}). Le client est EN LIGNE. Si la machine est éteinte/hors ligne, l'alerte bascule en AVERAGE (trigger "(client hors ligne)"), ce qui résout cette alerte HIGH et ferme le ticket GLPI associé. Seuil HIGH = {$URBACKUP_IMAGE_AGE_HIGH} secondes ({$URBACKUP_IMAGE_AGE_HIGH_LABEL}). Le backup image est le seul moyen de restaurer le système complet en cas de panne matérielle. Sans backup image récent, une restauration bare-metal est impossible. Actions urgentes : - Vérifier l'état du client et de l'agent UrBackup - Consulter les logs VSS si client Windows Server - Vérifier l'espace disque sur le serveur UrBackup - Forcer un backup image depuis l'interface UrBackup |
| UrBackup: [{#CLIENT_NAME}] Sauvegarde bloquée depuis +{$URBACKUP_STUCK_HIGH_DAYS} — intervenir | Élevé | Le backup de {#CLIENT_NAME} (IP: {#CLIENT_IP}) est en cours depuis plus de 5 jours et la progression (backup_pc) n'a pas bougé depuis 1h. Situation critique : un backup bloqué aussi longtemps mobilise des ressources (réseau, disque, verrous fichiers) et masque un vrai problème de sauvegarde. Actions urgentes : - Ouvrir l'interface UrBackup → Activités → vérifier la vitesse (speed_bpms) - Si vitesse = 0 B/s depuis > 1h : le backup est mort — interrompre via UrBackup - Vérifier la connectivité réseau avec {#CLIENT_NAME} (ping {#CLIENT_IP}) - Contrôler les verrous fichiers sur le client (handle.exe / lsof) - Après interruption : lancer manuellement un nouveau backup depuis UrBackup - Consulter les logs UrBackup (/var/log/urbackup/ ou interface → Logs) pour la cause |
| UrBackup: API indisponible sur {HOST.NAME} | Élevé | Aucune donnée reçue de l'API UrBackup depuis {$URBACKUP_NO_DATA_TIMEOUT}. Causes possibles : - Service urbackup-server arrêté sur {HOST.NAME} - Port {$URBACKUP_PORT} inaccessible (pare-feu, réseau) - Credentials incorrects (macros {$URBACKUP_USER} / {$URBACKUP_PASS}) - Timeout PBKDF2 dépassé (augmenter le timeout de l'item si nécessaire) Actions : - Vérifier le service : systemctl status urbackupsrv - Tester l'accès : curl http://{$URBACKUP_SERVER}:{$URBACKUP_PORT}/x?a=salt - Contrôler les macros dans Zabbix (Administration → Hôtes → {HOST.NAME}) Tous les triggers par client dépendent de ce trigger pour éviter les floods d'alertes. |
| UrBackup: {ITEM.LASTVALUE1} serveur(s)/Linux avec backup fichier en échec | Élevé | {ITEM.LASTVALUE1} serveur(s) ou machine(s) Linux EN LIGNE ont leur dernier backup fichier en échec. Serveurs concernés : {{?last(/{HOST.HOST}/urbackup.server.file.failures.list)}} Priorité HIGH car il s'agit de serveurs de production EN LIGNE — la perte de données serait critique. Note : seuls les serveurs EN LIGNE sont comptés ici. Si un serveur en échec est éteint (hors ligne), il sort de ce compteur et bascule sur le trigger AVERAGE "serveur(s)/Linux HORS LIGNE avec backup fichier en échec". Ce trigger HIGH se résout alors automatiquement, ce qui ferme le ticket GLPI associé. Causes fréquentes : - Client hors ligne ou agent UrBackup arrêté - Répertoires sources inaccessibles (permissions, partages réseau déconnectés) - Quota disque dépassé sur le serveur UrBackup - Erreur réseau pendant le transfert Actions immédiates : - Identifier les serveurs en échec dans l'interface UrBackup - Vérifier que l'agent UrBackup est démarré sur le serveur concerné - Consulter les logs UrBackup pour le détail de l'erreur - Lancer manuellement un backup depuis l'interface UrBackup |
| UrBackup: [{#CLIENT_NAME}] Pas de backup fichier depuis >{$URBACKUP_BACKUP_AGE_HIGH_LABEL} (client hors ligne) | Moyen | Aucun backup fichier depuis plus de {$URBACKUP_BACKUP_AGE_HIGH_LABEL} pour {#CLIENT_NAME} (IP: {#CLIENT_IP}), ET le client est actuellement HORS LIGNE (machine éteinte ou injoignable). Sévérité abaissée à AVERAGE (vs HIGH lorsque le client est en ligne) : une machine éteinte ne peut pas être sauvegardée, la situation est donc attendue tant qu'elle reste hors ligne. Basculement HIGH → AVERAGE : lorsque le serveur passe hors ligne, le trigger HIGH "Pas de backup fichier depuis >{$URBACKUP_BACKUP_AGE_HIGH_LABEL}" se résout (condition online=1 devenue fausse), ce qui ferme le ticket GLPI HIGH associé. Ce trigger AVERAGE prend alors le relais (online=0) en moindre priorité. Actions : - Confirmer que l'arrêt du client est volontaire (maintenance, mise hors service) - Si la machine doit rester en service : la rallumer pour reprendre les backups - Si la machine est décommissionnée : supprimer le client dans UrBackup |
| UrBackup: [{#CLIENT_NAME}] Pas de backup image depuis >{$URBACKUP_IMAGE_AGE_HIGH_LABEL} (client hors ligne) | Moyen | Aucun backup image depuis plus de {$URBACKUP_IMAGE_AGE_HIGH_LABEL} pour {#CLIENT_NAME} (IP: {#CLIENT_IP}), ET le client est actuellement HORS LIGNE (machine éteinte ou injoignable). Sévérité abaissée à AVERAGE (vs HIGH lorsque le client est en ligne) : une machine éteinte ne peut pas être sauvegardée, la situation est donc attendue tant qu'elle reste hors ligne. Basculement HIGH → AVERAGE : lorsque le serveur passe hors ligne, le trigger HIGH "Pas de backup image depuis >{$URBACKUP_IMAGE_AGE_HIGH_LABEL}" se résout (condition online=1 devenue fausse), ce qui ferme le ticket GLPI HIGH associé. Ce trigger AVERAGE prend alors le relais (online=0) en moindre priorité. Actions : - Confirmer que l'arrêt du client est volontaire (maintenance, mise hors service) - Si la machine doit rester en service : la rallumer pour reprendre les backups - Si la machine est décommissionnée : supprimer le client dans UrBackup |
| UrBackup: [{#CLIENT_NAME}] Sauvegarde possiblement bloquée depuis +{$URBACKUP_STUCK_WARN_DAYS} | Moyen | Le backup de {#CLIENT_NAME} (IP: {#CLIENT_IP}) est en cours depuis plus de 3 jours et la progression n'a pas changé depuis 1h. Un premier backup full ou un gros volume peut légitimement durer plusieurs jours. Cependant, une progression figée pendant 1h est anormale. Actions : - Vérifier l'interface UrBackup → backup en cours → vitesse de transfert - Si vitesse nulle : problème réseau ou fichier bloquant le transfert - Si vitesse faible mais positive : backup lent, surveiller avant d'intervenir - Si aucune progression dans les prochaines heures → trigger HIGH se déclenche à 5j |
| UrBackup: {ITEM.LASTVALUE1} client(s) avec backup image en échec | Moyen | {ITEM.LASTVALUE1} serveur(s)/Linux ont leur dernier backup image en échec. Le backup image (snapshot disque complet) est critique pour la restauration bare-metal. Un échec peut être dû à : - Espace disque insuffisant sur le serveur UrBackup - Client hors ligne au moment du backup planifié - Erreur VSS (Volume Shadow Copy) côté client Windows Server - Droits insuffisants de l'agent UrBackup Actions : - Identifier les clients en échec dans le dashboard UrBackup - Consulter les logs : /var/log/urbackup/ (serveur) ou Event Viewer (client) - Vérifier l'espace disque disponible sur le serveur UrBackup |
| UrBackup: {ITEM.LASTVALUE1} poste(s) de travail avec backup fichier en échec | Moyen | {ITEM.LASTVALUE1} poste(s) de travail Windows ont leur dernier backup fichier en échec. Causes fréquentes : - Poste éteint ou en veille prolongée au moment du backup planifié - Agent UrBackup arrêté ou non installé - Profil utilisateur verrouillé empêchant l'accès aux fichiers Actions : - Vérifier que le poste est allumé et l'agent UrBackup actif - Consulter l'interface UrBackup pour le détail de l'erreur par poste - Planifier les backups aux heures où les postes sont allumés |
| UrBackup: {ITEM.LASTVALUE1} serveur(s)/Linux HORS LIGNE avec backup fichier en échec | Moyen | {ITEM.LASTVALUE1} serveur(s) ou machine(s) Linux HORS LIGNE ont leur dernier backup fichier en échec. Serveurs concernés : {{?last(/{HOST.HOST}/urbackup.server.file.failures.offline.list)}} Sévérité AVERAGE (et non HIGH) : ces serveurs sont actuellement éteints / injoignables, ils ne peuvent donc pas être sauvegardés. La situation est attendue tant que la machine reste hors ligne. Lorsqu'un serveur en échec passe hors ligne, il bascule du compteur HIGH "serveurs en ligne" vers ce compteur AVERAGE : le trigger HIGH se résout et le ticket GLPI associé est fermé. Actions : - Confirmer que l'arrêt du/des serveur(s) est volontaire (maintenance, mise hors service) - Si le serveur doit rester en service : le rallumer pour reprendre les backups - Si le serveur est décommissionné : supprimer le client dans UrBackup |
| UrBackup: [{#CLIENT_NAME}] Pas de backup fichier depuis >{$URBACKUP_BACKUP_AGE_WARN_LABEL} | Avertissement | Aucun backup fichier depuis plus de {$URBACKUP_BACKUP_AGE_WARN_LABEL} pour {#CLIENT_NAME} (IP: {#CLIENT_IP}). Aucun backup en cours — le client n'a pas été sauvegardé dans les délais normaux. Seuil WARNING = {$URBACKUP_BACKUP_AGE_WARN} secondes ({$URBACKUP_BACKUP_AGE_WARN_LABEL}). Si la situation persiste, le trigger HIGH se déclenchera à {$URBACKUP_BACKUP_AGE_HIGH_LABEL}. Actions : - Vérifier que le client est en ligne - Contrôler la planification des backups dans UrBackup - Vérifier les logs pour un éventuel échec silencieux |
| UrBackup: [{#CLIENT_NAME}] Pas de backup image depuis >{$URBACKUP_IMAGE_AGE_WARN_LABEL} | Avertissement | Aucun backup image depuis plus de {$URBACKUP_IMAGE_AGE_WARN_LABEL} pour {#CLIENT_NAME} (IP: {#CLIENT_IP}). Seuil WARNING = {$URBACKUP_IMAGE_AGE_WARN} secondes ({$URBACKUP_IMAGE_AGE_WARN_LABEL}). Si la situation persiste, le trigger HIGH se déclenchera à {$URBACKUP_IMAGE_AGE_HIGH_LABEL}. Actions : - Vérifier la planification du backup image dans UrBackup - Contrôler les logs pour un éventuel échec du backup image précédent |

## Tableaux de bord (dashboards)

- UrBackup — Vue globale

---
*Documentation générée automatiquement à partir de l'export Zabbix.*
