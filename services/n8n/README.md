# n8n

Plateforme d'automatisation de workflows.

| Élément    | Valeur                                         |
| ---------- | ---------------------------------------------- |
| Image      | `docker.n8n.io/n8nio/n8n:latest`               |
| Conteneur  | `n8n`                                          |
| Port       | `127.0.0.1:5678` → `5678` (boucle locale)      |
| Accès      | `tailscale serve`, port HTTPS 8443, tailnet uniquement |
| Déploiement| Compose : `~/n8n/docker-compose.yml` (à copier ici) |
| Installé   | 2026-11-09                                     |

## Exposition réseau
Initialement publié sur toutes les interfaces (`0.0.0.0` et `[::]`), ce qui
contournait UFW (Docker écrit ses propres règles iptables). Le port est désormais
lié à `127.0.0.1` (constaté avec `docker ps` le 2026-11-09).

## À faire
- [x] Restreindre le port (`127.0.0.1:5678:5678`).
- [ ] Configurer `N8N_HOST`, `N8N_PROTOCOL`, `WEBHOOK_URL` si des webhooks sont utilisés (sinon les URL affichent `localhost`).
- [ ] Ajouter `docker-compose.yml` (sans secrets) et `.env.example`.
- [ ] Déplacer le Compose vers `/opt/homelab/compose/n8n/` (cohérence).
- [ ] Remplacer `:latest` par une version figée.
- [ ] Vérifier le volume de données (workflows, identifiants chiffrés) et l'inclure aux sauvegardes.
- [ ] Surveiller la RAM (`docker stats`) avec Ollama en parallèle.
