# Samba

Partage de fichiers sur le réseau local (service système, pas de conteneur).

| Élément | Valeur                         |
| ------- | ------------------------------ |
| Service | `smbd` actif, `nmbd` inactif   |
| Config  | `/etc/samba/smb.conf`          |
| Pare-feu| Autorisé depuis le LAN (UFW)   |

## Configuration ([`smb.conf`](smb.conf))
- Écoute uniquement sur `127.0.0.1` et `192.168.1.35`, clients limités à `192.168.1.0/24`.
- Pas d'accès invité, SMB2 minimum, port 445 seul.

| Partage | Chemin | Accès | Droits fichiers / dossiers |
| --- | --- | --- | --- |
| `Utilisateur1` | `/opt/homelab/data/samba/utilisateur1` | `utilisateur1` | 0600 / 0700 |
| `Utilisateur2` | `/opt/homelab/data/samba/utilisateur2` | `utilisateur2` | 0600 / 0700 |
| `Partage` | `/opt/homelab/data/samba/partage` | groupe `partage-groupe` | 0660 / 2770 (setgid) |

## À faire
- [x] `smb.conf` ajouté.
- [x] Partages, chemins et droits documentés.
- [ ] Inclure `/opt/homelab/data/samba` dans la stratégie de sauvegarde (données personnelles).
- [ ] Décrire un test d'accès depuis un autre poste.
