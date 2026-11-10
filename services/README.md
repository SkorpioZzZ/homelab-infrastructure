# Services

Un dossier par service : configuration de déploiement (sans secrets) et fiche d'exploitation.

| Service | Type | Dossier |
| --- | --- | --- |
| Open WebUI | Docker | [`open-webui/`](open-webui/) |
| Ollama | Docker | [`ollama/`](ollama/) |
| Portainer CE | Docker | [`portainer/`](portainer/) |
| Samba | Système | [`samba/`](samba/) |
| SSH | Système | [`ssh/`](ssh/) |
| n8n | Docker | [`n8n/`](n8n/) |

Règle : aucun mot de passe, jeton ni `.env` réel. Utiliser `.env.example`.
