# Portainer CE

Interface graphique d'administration de Docker.

| Élément    | Valeur                                   |
| ---------- | ---------------------------------------- |
| Image      | `portainer/portainer-ce:lts`             |
| Conteneur  | `portainer`                              |
| Port       | `127.0.0.1:9443` → `9443` (HTTPS, boucle locale) |
| Accès      | `tailscale serve`, port HTTPS 10000, tailnet uniquement |
| Volume     | `portainer_data`                         |
| Socket     | `/var/run/docker.sock` monté (bind)       |
| Restart    | `always`                                 |
| Déploiement| `docker run` (recréé le 2026-11-09) ; `compose.yaml` équivalent |

## Sécurité
Portainer a accès au socket Docker, donc un contrôle quasi total du serveur.
Ne jamais l'exposer sur Internet : accès uniquement via Tailscale.

## À faire
- [x] `compose.yaml` reconstitué et aligné sur le conteneur actuel.
- [x] Port restreint à `127.0.0.1`.
- [ ] Adopter Compose pour le déploiement (au lieu de `docker run`).
