# Hack the Cookie — Writeup

Writeup du lab **"Hack the Cookie"** (HackerDNA), portant sur une élévation de privilèges par manipulation d'un cookie de session encodé en Base64.

## 🎯 Résumé

Le portail interne fictif *TechCorp* stocke le rôle de l'utilisateur (`guest` / `admin`) dans un cookie `user_session`, encodé en Base64 mais **non signé et non vérifié côté serveur**. En décodant, modifiant puis ré-encodant ce cookie, un utilisateur invité peut s'octroyer les droits administrateur sans authentification supplémentaire.

**Catégorie :** Broken Access Control / Session Management
**Difficulté :** Débutant
**Outils utilisés :** navigateur (DevTools), terminal (`base64`)

## 📁 Contenu du dépôt

| Fichier | Description |
|---|---|
| `Hack_the_Cookie_EN.md` | Writeup original (anglais) |
| `Hack_the_Cookie_FR.md` | Traduction française |
| `images/` | Captures d'écran des différentes étapes |

## 🔍 Méthodologie

1. **Reconnaissance** — connexion au portail avec le compte `guest` fourni.
2. **Inspection** — récupération du cookie `user_session` via les DevTools du navigateur.
3. **Analyse** — décodage Base64 du cookie, révélant un objet JSON en clair (`user_id`, `username`, `role`, `email`).
4. **Exploitation** — modification du champ `role` de `guest` à `admin`, puis ré-encodage en Base64.
5. **Validation** — remplacement du cookie dans le navigateur et rechargement de la page → accès admin obtenu, flag récupéré.

## ⚠️ Cause racine

- Les données de session sont stockées **côté client**, sans mécanisme d'intégrité (pas de signature HMAC, pas de JWT vérifié).
- Le serveur fait **confiance aveuglément** au contenu du cookie sans revalider le rôle en base de données.
- Base64 est un **encodage**, pas un chiffrement : il ne protège ni la confidentialité ni l'intégrité des données.

## 🛠️ Remédiation

- Ne jamais stocker de données d'autorisation sensibles (rôle, droits) directement dans un cookie côté client.
- Utiliser un identifiant de session opaque, avec l'état réel (rôle, permissions) conservé côté serveur.
- Si un token client est nécessaire, utiliser un **JWT signé** (HMAC/RSA) et vérifier systématiquement la signature côté serveur.
- Ajouter les flags `HttpOnly`, `Secure` et `SameSite` sur les cookies de session.

## 📜 Disclaimer

Ce writeup est fourni à des fins strictement éducatives, dans le cadre d'un lab autorisé. Ne jamais appliquer ces techniques sur des systèmes réels sans autorisation explicite.

## 👤 Auteur

Ibrahim — MBA Expert en Cybersécurité (STUDI)
