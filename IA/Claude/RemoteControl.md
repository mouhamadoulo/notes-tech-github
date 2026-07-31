Oui. La méthode officielle est d'utiliser **Remote Control**, qui permet de continuer une session Claude Code depuis ton téléphone tout en laissant le code s'exécuter sur ton ordinateur. ([Claude][1])

Voici comment faire :

1. Vérifie que tu as une version récente de Claude Code :

   ```bash
   claude --version
   ```

2. Lance une session avec Remote Control :

   ```bash
   claude --remote-control
   ```

   ou, si une session est déjà ouverte :

   ```text
   /remote-control
   ```

   Un QR code et une URL de connexion s'affichent. ([Claude][1])

3. Sur ton téléphone :

   * ouvre l'application Claude (iOS ou Android), ou
   * ouvre Claude dans un navigateur,
   * scanne le QR code ou sélectionne la session dans l'onglet **Code**. ([Claude][1])

Tu pourras alors :

* envoyer de nouveaux prompts ;
* suivre la progression des tâches ;
* répondre aux demandes de confirmation de Claude ;
* consulter les modifications en cours.

Le code continue de s'exécuter **sur ton ordinateur**, pas sur le téléphone. ([Claude][1])

### Astuce pratique

Si tu utilises souvent cette fonctionnalité, tu peux l'activer automatiquement pour chaque nouvelle session via :

```text
/config
```

puis activer **Enable Remote Control for all sessions**. ([Claude][1])

Si ton objectif est de **piloter complètement ton PC depuis le téléphone** (terminal, VS Code, etc.), une autre approche consiste à utiliser un logiciel de bureau à distance comme Microsoft Remote Desktop, AnyDesk ou Tailscale + SSH. Cela donne un contrôle total de la machine, au-delà de Claude Code.

[1]: https://code.claude.com/docs/fr/remote-control?utm_source=chatgpt.com "Continuer les sessions locales depuis n'importe quel appareil avec Remote Control - Claude Code Docs"
