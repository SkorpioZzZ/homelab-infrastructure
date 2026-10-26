# Maintenance du homelab

## 1. Objectif

Ce document décrit les opérations de maintenance courantes du serveur Debian GNU/Linux 13 et des services auto-hébergés.

L'objectif est de vérifier l'état de l'infrastructure, de diagnostiquer les incidents courants et de limiter les risques lors des mises à jour.

## 2. Vérification de l'état du serveur

Les commandes suivantes permettent de contrôler les ressources et l'état général du système.

| Contrôle                 | Commande                  |
| ------------------------ | ------------------------- |
| Version du système       | `cat /etc/os-release`     |
| Charge et mémoire        | `uptime` et `free -h`     |
| Espace disque            | `df -h`                   |
| État des services Docker | `docker ps`               |
| État du pare-feu         | `sudo ufw status verbose` |
| État du service SSH      | `systemctl status ssh`    |

Ces vérifications permettent notamment de repérer un manque d'espace disque, une consommation excessive de mémoire ou un service indisponible.

## 3. Maintenance des conteneurs Docker

### Vérifier les conteneurs

```bash
docker ps -a
```

Cette commande affiche les conteneurs actifs et arrêtés, ainsi que leur état.

### Consulter les journaux

```bash
docker logs --tail 100 open-webui
docker logs --tail 100 ollama
docker logs --tail 100 portainer
```

Les journaux permettent de rechercher des erreurs au démarrage ou pendant l'exécution des services.

### Vérifier la consommation des ressources

```bash
docker stats --no-stream
```

Cette commande donne un aperçu de l'utilisation du processeur, de la mémoire et du réseau par les conteneurs.

## 4. Mises à jour

### Mettre à jour Debian

Avant une mise à jour, vérifier l'espace disque disponible et s'assurer que les données importantes sont protégées.

```bash
sudo apt update
apt list --upgradable
```

Après examen des mises à jour proposées, appliquer celles qui sont appropriées :

```bash
sudo apt upgrade
```

Contrôler ensuite l'état du système et des services.

### Images Docker

Avant de mettre à jour une image, vérifier les changements annoncés et prévoir une méthode de retour arrière adaptée. Une nouvelle image peut modifier le comportement d'un service.

Les procédures exactes de mise à jour d'Ollama, d'Open WebUI et de Portainer restent à formaliser selon leur méthode de déploiement respective.

## 5. Sauvegardes et restauration

Les sauvegardes sont essentielles pour protéger les données persistantes et la configuration du serveur.

Les volumes Docker identifiés comprennent notamment :

- `ollama_data`
- `open-webui_open_webui_data`
- `portainer_data`

La présence de ces volumes ne constitue pas, à elle seule, une sauvegarde.

**État actuel :** la stratégie de sauvegarde et la procédure de restauration restent à définir et à tester. Tant qu'une copie indépendante et une restauration vérifiée ne sont pas disponibles, les données doivent être considérées comme vulnérables à une panne du disque ou à une erreur de manipulation.

Une procédure complète devra préciser les données à sauvegarder, l'emplacement des copies, leur fréquence et la méthode de restauration.

## 6. Diagnostic d'un incident

En cas de dysfonctionnement :

1. Vérifier l'état du serveur, la mémoire et l'espace disque.
2. Vérifier l'état des conteneurs avec `docker ps -a`.
3. Examiner les journaux du service concerné.
4. Vérifier la connectivité réseau et les ports publiés.
5. Identifier la cause probable avant de redémarrer ou de modifier la configuration.
6. Après intervention, vérifier que le service fonctionne de nouveau.

Éviter de supprimer un conteneur, un volume ou des données persistantes sans avoir évalué les conséquences.

## 7. Sécurité

- Conserver l'accès SSH limité au réseau autorisé.
- Vérifier les règles du pare-feu après toute modification réseau.
- Ne pas publier de mots de passe, de jetons, de clés privées ou de fichiers `.env`.
- Vérifier les ports exposés par les conteneurs avant tout changement.
- Ne pas considérer un service accessible sur le réseau local comme automatiquement sécurisé.
- Consulter les journaux en masquant les informations sensibles avant de les partager.

## 8. Améliorations prévues

- Mettre en place des sauvegardes indépendantes des données persistantes et de la configuration.
- Tester une restauration réelle.
- Formaliser les mises à jour et le retour arrière des images Docker.
- Définir une routine de vérification périodique.
- Documenter les procédures de reprise après incident.
