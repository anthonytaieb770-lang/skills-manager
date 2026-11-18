---
name: openjarvis
description: Utiliser, diagnostiquer, mettre à jour ou désinstaller OpenJarvis, l'assistant IA local (open-jarvis/OpenJarvis) installé sur le Mac dans ~/.openjarvis. À utiliser dès que l'utilisateur parle de « Jarvis », d'« OpenJarvis », d'IA locale / hors-ligne sur son Mac, de la commande `jarvis`, ou demande de brancher un compte, un preset ou un skill sur OpenJarvis. Contient les règles de sécurité issues de l'audit du 18/11/2026.
---

## Ce que c'est

OpenJarvis est un assistant IA qui tourne sur la machine (modèles locaux via
Ollama), licence Apache-2.0. Version alpha : utile pour la confidentialité et
le hors-ligne, nettement moins fort qu'un modèle cloud pour le raisonnement.

- Tout est dans `~/.openjarvis` (code, environnement Python, config, journaux).
- Les modèles Ollama sont dans `~/.ollama`.
- La commande `jarvis` est dans `~/.local/bin` (ligne PATH ajoutée à `~/.zshrc`).

## Avant d'agir

1. Vérifie que c'est installé : `command -v jarvis || ls ~/.openjarvis`.
   Absent : voir « Installation ».
2. En cas de souci, commence toujours par `jarvis doctor` et lis sa sortie
   avant de proposer une cause.

## Commandes courantes

| Besoin | Commande |
|---|---|
| Discuter | `jarvis` |
| Une question ponctuelle | `jarvis ask "..."` |
| Interface graphique (Node.js 22+ requis) | `jarvis gui` |
| État de l'installation | `jarvis doctor` |
| Changer de preset | `jarvis init --preset <nom> --force` |
| Couper les statistiques d'usage | `jarvis config set analytics.enabled false` |

Presets : `chat-simple` (défaut, sans outils), `deep-research`,
`morning-digest-mac`, `scheduled-monitor`, `code-assistant`.

## Règles de sécurité (ne pas contourner sans accord explicite)

- **Statistiques d'usage (PostHog)** : doivent rester désactivées. Vérifie
  `[analytics] enabled = false` dans la config après toute réinstallation ou
  `jarvis init`. À l'invite de consentement, Entrée vaut « oui » : répondre N.
- **Minage de cryptomonnaie** : ne jamais lancer `jarvis mine` ni
  `jarvis pearl`, ne jamais écrire de section `[mining]` dans la config.
- **Comptes** : `jarvis connect ...` (Gmail, Drive, Slack…) donne à un modèle
  local alpha l'accès aux données. Demander l'accord avant, et ne jamais y
  brancher le CRM Bat Énergie.
- **Skills communautaires** : `jarvis skill install` / `jarvis skill sync`
  (Hermes, OpenClaw) importent du contenu non audité. Lire le skill avant
  installation, un par un, jamais de synchronisation en masse.
- **Presets avec exécution de code** (`code-assistant`, agents `native_openhands`,
  `operative`) : ils lancent des commandes sur le Mac. Accord préalable requis.
- **Réseau** : le serveur doit rester sur `127.0.0.1`. Jamais `--host 0.0.0.0`.

## Installation

Script audité, épinglé sur le commit vérifié, statistiques refusées :

```bash
curl -fsSL https://raw.githubusercontent.com/open-jarvis/OpenJarvis/a7a43557e14f8b8943672f6a1e4cae8d92008eec/scripts/install/install.sh -o /tmp/openjarvis-install.sh
bash /tmp/openjarvis-install.sh --no-analytics
```

Pas de sudo sur macOS, pas de service au démarrage. Prévoir ~15 Go libres.
Preuve attendue : `jarvis ask "..."` renvoie une vraie réponse.

## Mise à jour

L'audit ne couvre que le commit `a7a43557` (17/11/2026). Avant de mettre à
jour `~/.openjarvis/src` vers une version plus récente, relire ce qui a changé
dans `scripts/install/`, `src/openjarvis/analytics/`, `src/openjarvis/mining/`
et les dépendances (`pyproject.toml`), puis mettre à jour ce skill.

## Désinstallation

Action irréversible (supprime config et historique) : demander confirmation.

```bash
~/.openjarvis/.scripts/jarvis-uninstall.sh
```

Les modèles dans `~/.ollama` et Ollama lui-même restent en place ; les
supprimer séparément seulement si rien d'autre ne s'en sert.

## Limites connues de l'audit

Lecture statique du code, sans exécution. Dépendances tierces non auditées.
Rattachement à Stanford affiché par le projet mais non vérifié de façon
indépendante.
