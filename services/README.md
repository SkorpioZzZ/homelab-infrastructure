# Services

Un dossier par service : configuration de déploiement (sans secrets) et fiche d'exploitation.

| Service | Type | Dossier |
| --- | --- | --- |
| AdGuard Home | Docker | [`adguard-home/`](adguard-home/) |
| Portainer CE | Docker | [`portainer/`](portainer/) |
| Samba | Système | [`samba/`](samba/) |
| SSH | Système | [`ssh/`](ssh/) |
| n8n | Docker | [`n8n/`](n8n/) |

Règle : aucun mot de passe, jeton ni `.env` réel. Utiliser `.env.example`.

Ollama et Open WebUI ont été retirés le 2026-11-18 (voir l'historique Git).
