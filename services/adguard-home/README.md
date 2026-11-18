# AdGuard Home

Serveur DNS filtrant : bloque les publicités et les traqueurs en répondant `0.0.0.0` aux domaines listés.
Remplace Ollama et Open WebUI depuis le 2026-11-18 (modèles locaux trop lents sur ce matériel, service inutilisé).

| Élément     | Valeur                                                   |
| ----------- | -------------------------------------------------------- |
| Image       | `adguard/adguardhome:latest` (à figer)                   |
| Conteneur   | `adguard-home`                                           |
| Ports       | `53/tcp` et `53/udp` (toutes interfaces) ; `127.0.0.1:3000` (admin) |
| Volumes     | `adguard-home_adguard_work` (données), `adguard-home_adguard_conf` (configuration) |
| Restart     | `unless-stopped`                                         |
| Déploiement | Compose : `/opt/homelab/compose/adguard-home/compose.yaml` ([copie](compose.yaml)) |
| Installé    | 2026-11-18                                               |

## Choix de configuration

- **Port 53 ouvert sur toutes les interfaces** : exception volontaire à la règle « 127.0.0.1 uniquement », car un DNS doit être joignable par les appareils. Aucun port n'est redirigé sur la box : rien n'est exposé sur Internet.
- **Interface d'administration sur la boucle locale** : accès via `tailscale serve` (port HTTPS 9000, tailnet uniquement) ou tunnel SSH `ssh -N -L 3000:127.0.0.1:3000 <user>@<ip-serveur>`.
- **DNS amont chiffré** : Quad9 en DNS-over-HTTPS (`https://dns.quad9.net/dns-query`), amorçage `9.9.9.9`, DNSSEC activé.
- **Réécriture DNS** : `mabbox.bytel.fr` → IP de la box, sinon l'interface de la Bbox n'est plus joignable par son nom.
- **Listes de filtrage** : liste par défaut uniquement, pour pouvoir identifier la cause si un site casse.
- **Pas de DHCP** : la Bbox ne permet pas de choisir le DNS distribué (l'option 6 ne le place qu'en 2e position) et, même DHCP désactivé, elle annonce son DNS en IPv6. Confier le DHCP au serveur le rendrait en plus indispensable à la connexion de tous les appareils.

## Appareils couverts

| Appareil | Méthode |
| --- | --- |
| PC Linux (NetworkManager) | DNS manuel `<ip-serveur>`, DNS automatiques ignorés en IPv4 et IPv6 (`ipv4.ignore-auto-dns`, `ipv6.ignore-auto-dns`) |
| Appareils du tailnet (téléphone…) | Console Tailscale → DNS → nameserver = IP Tailscale du serveur, « Override DNS servers » activé |
| Serveur lui-même | **Non couvert volontairement** : `tailscale set --accept-dns=false`, il résout via la box pour rester autonome si AdGuard tombe |

## Vérifications

```bash
dig @<ip-serveur> doubleclick.net +short   # 0.0.0.0 → bloqué
dig @<ip-serveur> wikipedia.org +short     # vraies IP → résolution normale
docker logs --tail 50 adguard-home
```

Le journal des requêtes de l'interface montre quels appareils passent réellement par AdGuard.

## Point de vigilance

Les appareils configurés dépendent du serveur pour résoudre les noms. **Serveur éteint = plus d'Internet** sur ces appareils (et sur le téléphone tant que Tailscale est actif). Avant d'éteindre le serveur, couper Tailscale sur le téléphone.

Retour arrière sur un PC Linux :
```bash
nmcli connection modify "<connexion>" ipv4.dns "" ipv4.ignore-auto-dns no ipv6.ignore-auto-dns no
nmcli connection up "<connexion>"
```

## À faire
- [ ] Figer la version de l'image après quelques jours sans problème.
- [ ] Sauvegarder le volume de configuration.
- [x] IP du serveur réservée dans la box (règle statique DHCP IPv4).
