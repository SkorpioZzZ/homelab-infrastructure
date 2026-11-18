# Services et conteneurs

## 1. Vue d'ensemble

Le homelab héberge plusieurs services destinés à expérimenter le filtrage DNS, l'automatisation, la conteneurisation Docker et le partage de fichiers sur un réseau local.

Les services sont hébergés sur un Dell Latitude E5510 fonctionnant sous Debian GNU/Linux 13 (Trixie). L'ensemble doit rester adapté aux ressources limitées de cette machine : environ 3,6 Gio de mémoire vive détectée et un SSD de 250 Go.

## 2. Services déployés

| Service      | Technologie   | Fonction principale                           | État documenté                         |
| ------------ | ------------- | --------------------------------------------- | -------------------------------------- |
| AdGuard Home | Docker        | DNS filtrant (publicités, traqueurs)          | Conteneur actif, vérifié le 2026-11-18 |
| Portainer CE | Docker        | Administration graphique des conteneurs       | Conteneur actif lors du relevé         |
| Samba        | Service Linux | Partage de fichiers sur le réseau local       | Service `smbd` actif lors du relevé    |
| SSH          | Service Linux | Administration distante du serveur            | Service actif lors du relevé           |
| n8n          | Docker        | Automatisation de workflows                   | Conteneur actif lors du relevé         |
| Tailscale    | Service Linux | Accès distant chiffré (VPN maillé)            | Service actif, vérifié le 2026-11-09   |

Ces états correspondent aux observations réalisées pendant la documentation. Ils ne garantissent pas que les services seront toujours disponibles.

## 3. Filtrage DNS

### AdGuard Home

**Image Docker :** `adguard/adguardhome`

AdGuard Home est un serveur DNS : quand un appareil demande l'adresse d'un domaine publicitaire ou de pistage, il répond `0.0.0.0`, et la ressource n'est jamais chargée. Les autres requêtes sont transmises chiffrées (DNS-over-HTTPS) à Quad9.

Configuration observée :

- Conteneur Docker nommé `adguard-home`, déployé avec Docker Compose.
- Port `53` (TCP/UDP) publié sur toutes les interfaces : exception volontaire, nécessaire pour que les appareils l'interrogent. Aucune redirection sur la box.
- Interface d'administration sur `127.0.0.1:3000`, accessible via `tailscale serve` (port 9000, tailnet uniquement).
- Volumes `adguard-home_adguard_work` (données) et `adguard-home_adguard_conf` (configuration).
- Politique de redémarrage `unless-stopped`.
- Pas de serveur DHCP : la Bbox ne permet pas de choisir le DNS distribué, les appareils sont donc configurés un par un ou via Tailscale.

Détails et choix : [`services/adguard-home/`](../services/adguard-home/README.md).

### Historique : IA locale (retirée)

Ollama et Open WebUI ont été installés pour expérimenter l'IA locale, puis supprimés le 2026-11-18 : avec environ 3,6 Gio de RAM, seuls de très petits modèles tournaient, trop lents pour un usage réel.

## 4. Administration des conteneurs

### Portainer CE

**Image Docker :** `portainer/portainer-ce:lts`

Portainer fournit une interface graphique pour consulter et administrer les ressources Docker.

Configuration observée :

- Conteneur nommé `portainer`.
- Port hôte `127.0.0.1:9443`, redirigé vers le port `9443` du conteneur (conteneur recréé le 2026-11-09).
- Réseau Docker `bridge`.
- Volume Docker nommé `portainer_data` pour la persistance des données.
- Politique de redémarrage `always`.

L'interface n'est publiée que sur la boucle locale. L'accès distant passe par `tailscale serve`, réservé aux appareils du tailnet.

### n8n

**Image Docker :** `docker.n8n.io/n8nio/n8n`

Plateforme d'automatisation de workflows. Port hôte `127.0.0.1:5678`, accès distant via `tailscale serve`. Détails : [`services/n8n/`](../services/n8n/README.md).

## 5. Partage de fichiers et administration distante

### Samba

Samba permet le partage de fichiers entre le serveur Linux et les machines du réseau local.

Le service `smbd` était actif lors du relevé. Le service `nmbd` était inactif.

Les éléments suivants restent à documenter à partir de la configuration réelle :

- Répertoires partagés et droits d'accès.
- Comptes autorisés et permissions.
- Configuration des partages.
- Procédure de test depuis un autre ordinateur.

Aucun chemin de partage ni compte utilisateur n'est supposé dans cette documentation.

### SSH

SSH permet d'administrer le serveur à distance depuis un terminal.

L'authentification par clé SSH est configurée et les règles UFW relevées limitent les connexions SSH au réseau local.

## 6. Déploiement Docker Compose

Les fichiers Compose sont conservés sur le serveur, une copie sans secrets est versionnée dans [`services/`](../services/README.md) :

| Service      | Fichier sur le serveur                               |
| ------------ | ---------------------------------------------------- |
| AdGuard Home | `/opt/homelab/compose/adguard-home/compose.yaml`     |
| n8n          | `~/n8n/docker-compose.yml` (à déplacer dans `/opt/homelab/compose/`) |
| Portainer CE | Lancé par `docker run` ; `compose.yaml` reconstitué  |

Les fichiers de configuration publiés dans le dépôt doivent être vérifiés afin d'exclure les mots de passe, jetons, clés privées et autres données sensibles.

## 7. Stockage persistant

Les volumes Docker suivants ont été identifiés :

| Volume                       | Service associé | Utilité                                   |
| ---------------------------- | --------------- | ----------------------------------------- |
| `adguard-home_adguard_work`  | AdGuard Home    | Statistiques et journal des requêtes      |
| `adguard-home_adguard_conf`  | AdGuard Home    | Configuration (`AdGuardHome.yaml`)        |
| `n8n_n8n_data`               | n8n             | Workflows et identifiants chiffrés        |
| `portainer_data`             | Portainer CE    | Conservation des données d'administration |

La présence de ces volumes permet de séparer les données persistantes du cycle de vie des conteneurs. Elle ne constitue pas une sauvegarde : une stratégie de sauvegarde et une procédure de restauration restent à définir et à tester.

## 8. Pistes d'amélioration

- Figer la version de l'image AdGuard Home.
- Documenter la configuration réelle des partages Samba.
- Décrire la méthode de déploiement de chaque conteneur.
- Définir et tester une procédure de sauvegarde et de restauration.
- Documenter les procédures de mise à jour et de dépannage.

Cette documentation décrit les éléments observés sur l'installation. Les points restant à vérifier sont explicitement signalés afin de distinguer les faits constatés des améliorations envisagées.
