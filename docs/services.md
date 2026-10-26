# Services et conteneurs

## 1. Vue d'ensemble

Le homelab héberge plusieurs services destinés à expérimenter l'intelligence artificielle locale, la conteneurisation Docker et le partage de fichiers sur un réseau local.

Les services sont hébergés sur un Dell Latitude E5510 fonctionnant sous Debian GNU/Linux 13 (Trixie). L'ensemble doit rester adapté aux ressources limitées de cette machine : environ 3,6 Gio de mémoire vive détectée et un SSD de 250 Go.

## 2. Services déployés

| Service      | Technologie   | Fonction principale                           | État documenté                         |
| ------------ | ------------- | --------------------------------------------- | -------------------------------------- |
| Ollama       | Docker        | Exécution locale de modèles de langage        | Conteneur actif lors du relevé         |
| Open WebUI   | Docker        | Interface web pour interagir avec les modèles | Conteneur actif et sain lors du relevé |
| Portainer CE | Docker        | Administration graphique des conteneurs       | Conteneur actif lors du relevé         |
| Samba        | Service Linux | Partage de fichiers sur le réseau local       | Service `smbd` actif lors du relevé    |
| SSH          | Service Linux | Administration distante du serveur            | Service actif lors du relevé           |

Ces états correspondent aux observations réalisées pendant la documentation. Ils ne garantissent pas que les services seront toujours disponibles.

## 3. Intelligence artificielle locale

### Ollama

**Image Docker :** `ollama/ollama`

Ollama permet d'exécuter des modèles de langage directement sur le serveur. Il constitue le moteur d'inférence utilisé par Open WebUI.

Configuration observée :

- Conteneur Docker nommé `ollama`.
- Port hôte `127.0.0.1:11434`, redirigé vers le port `11434` du conteneur.
- Réseau Docker `ia_interne`, partagé avec Open WebUI.
- Présence également sur le réseau Docker `bridge`.
- Volume Docker nommé `ollama_data` pour la persistance des données.
- Politique de redémarrage `unless-stopped`.

Le port publié sur `127.0.0.1` est accessible depuis la boucle locale du serveur. Il n'est donc pas directement publié sur toutes les interfaces réseau de l'hôte.

La liste des modèles installés, leur taille et leurs performances restent à documenter.

### Open WebUI

**Image Docker :** `ghcr.io/open-webui/open-webui:main`

Open WebUI fournit une interface web permettant d'interagir avec les modèles disponibles via Ollama.

Configuration observée :

- Conteneur Docker nommé `open-webui`.
- Port hôte `127.0.0.1:3000`, redirigé vers le port `8080` du conteneur.
- Réseau Docker `ia_interne`.
- Volume Docker nommé `open-webui_open_webui_data` pour la persistance des données.
- Politique de redémarrage `unless-stopped`.
- Contrôle de santé Docker configuré ; le conteneur était déclaré sain lors du relevé.
- Paramètre `OLLAMA_BASE_URL` déclaré dans la configuration Compose pour définir l'adresse du service Ollama.
- Paramètre `WEBUI_AUTH` présent dans la configuration pour gérer le comportement de l'authentification.

Les valeurs des variables d'environnement et les éventuels identifiants ne sont pas publiés dans cette documentation.

Le port étant lié à la boucle locale du serveur, l'accès depuis un autre ordinateur peut nécessiter un tunnel SSH. Le fonctionnement actuel de cet accès distant reste à vérifier.

## 4. Administration des conteneurs

### Portainer CE

**Image Docker :** `portainer/portainer-ce:lts`

Portainer fournit une interface graphique pour consulter et administrer les ressources Docker.

Configuration observée :

- Conteneur nommé `portainer`.
- Port hôte lié à l'adresse LAN du serveur, sur le port `9443`, redirigé vers le port `9443` du conteneur.
- Réseau Docker `bridge`.
- Volume Docker nommé `portainer_data` pour la persistance des données.
- Politique de redémarrage `always`.

L'interface est publiée sur l'adresse LAN du serveur. Les règles réseau effectives et les conditions d'accès doivent être vérifiées séparément avant de tirer une conclusion sur son niveau de sécurité.

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

Le déploiement d'Open WebUI est défini dans un fichier Docker Compose conservé sur le serveur.

Il décrit notamment :

- L'image et le nom du conteneur.
- Les ports publiés.
- Les variables d'environnement.
- Le stockage persistant.
- Le réseau Docker `ia_interne`.
- Le contrôle de santé et la politique de redémarrage.

Ce fichier sert de référence pour comprendre le déploiement d'Open WebUI. La méthode de déploiement d'Ollama et de Portainer reste à documenter séparément si elle diffère.

Les fichiers de configuration publiés dans le dépôt doivent être vérifiés afin d'exclure les mots de passe, jetons, clés privées et autres données sensibles.

## 7. Stockage persistant

Les volumes Docker suivants ont été identifiés :

| Volume                       | Service associé | Utilité                                   |
| ---------------------------- | --------------- | ----------------------------------------- |
| `ollama_data`                | Ollama          | Conservation des données du service       |
| `open-webui_open_webui_data` | Open WebUI      | Conservation des données de l'interface   |
| `portainer_data`             | Portainer CE    | Conservation des données d'administration |

La présence de ces volumes permet de séparer les données persistantes du cycle de vie des conteneurs. Elle ne constitue pas une sauvegarde : une stratégie de sauvegarde et une procédure de restauration restent à définir et à tester.

## 8. Pistes d'amélioration

- Documenter la liste des modèles Ollama effectivement installés.
- Mesurer les temps de réponse et la consommation mémoire des modèles.
- Vérifier l'accès à Open WebUI depuis un autre poste.
- Documenter la configuration réelle des partages Samba.
- Décrire la méthode de déploiement de chaque conteneur.
- Définir et tester une procédure de sauvegarde et de restauration.
- Documenter les procédures de mise à jour et de dépannage.

Cette documentation décrit les éléments observés sur l'installation. Les points restant à vérifier sont explicitement signalés afin de distinguer les faits constatés des améliorations envisagées.
