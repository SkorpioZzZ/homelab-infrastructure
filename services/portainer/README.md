# Portainer CE

Interface graphique d'administration de Docker.

| Élément    | Valeur                                   |
| ---------- | ---------------------------------------- |
| Image      | `portainer/portainer-ce:lts`             |
| Conteneur  | `portainer`                              |
| Port       | `<ip-lan>:9443` → `9443` (HTTPS, réseau local) |
| Volume     | `portainer_data`                         |
| Socket     | `/var/run/docker.sock` monté (bind)       |
| Restart    | `always`                                 |
| Déploiement| Hors Compose ; `compose.yaml` reconstitué |

## Sécurité
Portainer a accès au socket Docker, donc un contrôle quasi total du serveur.
Ne jamais l'exposer hors du réseau local.

## À faire
- [x] `compose.yaml` reconstitué (non encore appliqué au serveur).
- [ ] Vérifier que le port 9443 n'est accessible que depuis le LAN.
