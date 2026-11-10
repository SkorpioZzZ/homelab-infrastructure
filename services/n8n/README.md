# n8n

Plateforme d'automatisation de workflows.

| Élément    | Valeur                                         |
| ---------- | ---------------------------------------------- |
| Image      | `docker.n8n.io/n8nio/n8n:latest`               |
| Conteneur  | `n8n`                                          |
| Port       | `0.0.0.0:5678` et `[::]:5678` → `5678` ⚠️       |
| Déploiement| Compose : `~/n8n/docker-compose.yml` (à copier ici) |
| Installé   | 2026-11-09                                     |

## ⚠️ Exposition réseau
Le port est publié sur **toutes les interfaces, IPv4 et IPv6**. Docker écrit ses
propres règles iptables : **UFW ne filtre pas ce port**. Lier à `127.0.0.1`
(accès par tunnel SSH, comme Open WebUI) ou à l'IP LAN uniquement.

## À faire
- [ ] Restreindre le port (`127.0.0.1:5678:5678`).
- [ ] Ajouter `docker-compose.yml` (sans secrets) et `.env.example`.
- [ ] Déplacer le Compose vers `/opt/homelab/compose/n8n/` (cohérence).
- [ ] Remplacer `:latest` par une version figée.
- [ ] Vérifier le volume de données (workflows, identifiants chiffrés) et l'inclure aux sauvegardes.
- [ ] Surveiller la RAM (`docker stats`) avec Ollama en parallèle.
