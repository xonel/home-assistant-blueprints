# Home Assistant Blueprints

Blueprints d'automatisation pour Home Assistant.

## Utilisation

1. Allez dans **Paramètres → Automatisations & scènes → Blueprints → Importer un blueprint**
2. Collez l'URL brute du fichier souhaité (ex: `https://raw.githubusercontent.com/xonel/home-assistant-blueprints/main/automation/ikea_rodret_switches_zha.yaml`)
3. Créez une automatisation à partir du blueprint importé

## Structure

- `automation/` — Blueprints d'automatisation
- `script/` — Blueprints de script
- `template/` — Blueprints de template

## Blueprints maison (M11)

### IKEA STYRBAR - 4 boutons, simple / double / triple clic (ZHA)

Fichier : [`blueprints/automation/m11/styrbar_4_boutons_multiclic.yaml`](blueprints/automation/m11/styrbar_4_boutons_multiclic.yaml)

Télécommande IKEA STYRBAR (E2001/E2002, ZHA « Remote Control N2 »), 4 boutons H/B/G/D :

- **Clic simple** : bascule du groupe (allume à 100 % ou éteint si au moins une entité est allumée)
- **Double clic** : bascule, avec allumage à 30 % (réglable) pour les lumières dimmables
- **Triple clic** : éteint le groupe du bouton
- **D** agit sur l'union des groupes H, B et G

Le comptage des clics est logiciel (fenêtre de 900 ms réglable) : le clic simple est exécuté après cette fenêtre. Le `on` parasite envoyé lors d'un appui long sur G/D est ignoré.

| Bouton | Clic net (zha_event) | Appui long |
|---|---|---|
| H | `on` (cluster 6) | `move_with_on_off [0,83]` puis `stop_with_on_off` |
| B | `off` (cluster 6) | `move [1,83,0,0]` puis `stop` |
| G | `press [257,13,0]` (cluster 5) | `release[0]`, `on` parasite, `recall`, `hold [3329,0]`, `release` |
| D | `press [256,13,0]` (cluster 5) | idem avec `hold [3328,0]` |

Limites : les switchs on/off ne sont pas dimmables (le double clic se limite à les allumer) ; un 4e clic est traité comme un nouveau clic simple ; les appuis longs ne sont pas gérés.
