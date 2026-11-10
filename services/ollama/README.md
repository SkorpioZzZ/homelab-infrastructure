# Ollama

Moteur d'exécution local de modèles de langage, utilisé par Open WebUI.

| Élément    | Valeur                                   |
| ---------- | ---------------------------------------- |
| Image      | `ollama/ollama`                          |
| Conteneur  | `ollama`                                 |
| Port       | `127.0.0.1:11434` → `11434`              |
| Volume     | `ollama_data`                            |
| Réseaux    | `ia_interne`, `bridge`                   |
| Healthcheck| Aucun                                    |
| Restart    | `unless-stopped`                         |
| Déploiement| Hors Compose ; `compose.yaml` reconstitué |

## Commandes
```bash
docker exec -it ollama ollama list
docker exec -it ollama ollama pull <modèle>
docker exec -it ollama ollama rm <modèle>
```

## Modèles installés (2026-11-09)
| Modèle | Taille |
| --- | --- |
| `llama3.2:1b` | 1,3 Go |

## Contraintes
~3,6 Gio de RAM sur le serveur : privilégier de petits modèles quantifiés.

## À faire
- [x] `compose.yaml` reconstitué (non encore appliqué au serveur).
- [ ] Mesurer les performances de `llama3.2:1b`.
- [ ] Retirer Ollama du réseau `bridge` : Open WebUI passe par `ia_interne`, le port 127.0.0.1:11434 reste publié. Le `compose.yaml` ne l'inclut pas.
