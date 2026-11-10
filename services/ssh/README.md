# SSH

Administration à distance du serveur (service système).

| Élément | Valeur                               |
| ------- | ------------------------------------ |
| Service | `ssh` actif                          |
| Auth    | Clé SSH configurée                   |
| Pare-feu| Autorisé depuis le LAN (UFW)         |
| Config  | `/etc/ssh/sshd_config` (+ `sshd_config.d/`) |

## Réglages effectifs (`sshd -T`, 2026-11-09)
| Paramètre | Valeur |
| --- | --- |
| `port` | 22 |
| `permitrootlogin` | no |
| `pubkeyauthentication` | yes |
| `passwordauthentication` | no |
| `x11forwarding` | yes |
| `allowusers` | non défini |

## À faire
- [x] `PasswordAuthentication no` et `PermitRootLogin no` vérifiés.
- [ ] Passer `X11Forwarding no` (inutile sur un serveur sans interface graphique).
- [ ] Ajouter `AllowUsers <utilisateur>` pour restreindre les comptes autorisés en SSH.
