# Open WebUI

Interface web pour discuter avec les modèles servis par Ollama.

| Élément    | Valeur                                   |
| ---------- | ---------------------------------------- |
| Image      | `ghcr.io/open-webui/open-webui:main`     |
| Conteneur  | `open-webui`                             |
| Port       | `127.0.0.1:3000` → `8080` (boucle locale uniquement) |
| Volume     | `open-webui_open_webui_data`             |
| Réseau     | `ia_interne`                             |
| Restart    | `unless-stopped`                         |
| Healthcheck| `GET http://127.0.0.1:8080/health` toutes les 30 s (timeout 10 s, 3 essais, démarrage 60 s) |
| Déploiement| Compose : `/opt/homelab/compose/open-webui/compose.yaml` ([copie](compose.yaml)) |

## Accès
Depuis un autre poste : tunnel SSH `ssh -N -L 3000:127.0.0.1:3000 <user>@<ip-serveur>`, puis http://127.0.0.1:3000.

## Variables (valeurs non publiées)
- `OLLAMA_BASE_URL` : adresse d'Ollama sur le réseau `ia_interne`.
- `WEBUI_AUTH` : active l'authentification.

## Mise à jour
```bash
docker compose pull && docker compose up -d && docker image prune
```
L'étiquette `:main` suit la branche de développement : sauvegarder le volume avant.

## À faire
- [x] `compose.yaml` réel ajouté.
- [x] `.env.example` créé.
- [ ] `WEBUI_SECRET_KEY` absent du Compose : vérifier comment il est généré/conservé (sinon sessions possiblement invalidées à chaque recréation).
- [ ] Envisager une version figée de l'image plutôt que `:main`.
