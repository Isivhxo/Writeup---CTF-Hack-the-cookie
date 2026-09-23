# Pirater le Cookie : Élévation de privilèges par manipulation de cookie

## Introduction

Bienvenue dans ce writeup détaillé du lab « Hack the Cookie » sur HackerDNA. Ce challenge illustre une vulnérabilité web courante : une gestion de session non sécurisée, exploitable par falsification d'un cookie côté client. En manipulant un cookie encodé en Base64, on peut élever ses privilèges d'un utilisateur invité (guest) jusqu'au rôle administrateur, contournant ainsi les contrôles d'authentification.

Le lab simule un portail interne d'entreprise où les rôles utilisateurs sont stockés dans un cookie modifiable. Nous allons voir, étape par étape, comment se connecter avec les identifiants fournis, inspecter et décoder le cookie, le modifier, puis le ré-encoder pour obtenir un accès administrateur.

Au démarrage du lab :

**Prérequis :**
- Un navigateur web avec outils de développement (ex. Chrome DevTools).
- Un accès à un terminal pour l'encodage/décodage Base64.
- L'URL du lab : https://hack-the-cookie.tiny.io

**Remarque :** Ceci est à des fins strictement éducatives, dans un environnement de lab contrôlé. Ne jamais appliquer ces techniques sur des systèmes réels sans autorisation.

## Étape 1 : Accéder au lab et se connecter

1. Se rendre sur l'URL du lab : https://hack-the-cookie.tiny.io.
2. Une page de connexion pour le « TechCorp Internal Portal » s'affiche.
3. Utiliser les identifiants pré-remplis :
   - Nom d'utilisateur : guest
   - Mot de passe : password
4. Cliquer sur « Login » pour accéder au tableau de bord invité. Une vue restreinte apparaît, avec des options limitées comme « Employee Resources » et « IT Support Ticket System ».

## Étape 2 : Inspecter le cookie de session via les outils de développement

1. Une fois le tableau de bord chargé, ouvrir les outils de développement (F12 ou clic droit > Inspecter).
2. Aller dans l'onglet « Application » (ou « Storage » selon le navigateur).
3. Dans « Cookies », repérer le cookie du domaine nommé `user_session`.
4. Copier la valeur de `user_session`. Elle doit ressembler à ceci (exemple) :

```
eyJ1c2VyX2lkIjoxLCJ1c2VybmFtZSI6Imd1ZXN0Iiwicm9sZSI6Imd1ZXN0IiwiZW1haWwiOiJndWVzdEB0ZWNoY29ycC5sb2NhbCJ9
```

## Étape 3 : Décoder le cookie

1. Ouvrir un terminal.
2. Utiliser la commande `base64` pour décoder la valeur copiée :

```bash
echo "eyJ1c2VyX2lkIjoxLCJ1c2VybmFtZSI6Imd1ZXN0Iiwicm9sZSI6Imd1ZXN0IiwiZW1haWwiOiJndWVzdEB0ZWNoY29ycC5sb2NhbCJ9" | base64 -d
```

3. Le résultat est un objet JSON révélant les détails de la session :

```json
{"user_id":1,"username":"guest","role":"guest","email":"guest@techcorp.local"}
```

Cela confirme que le cookie stocke les données utilisateur en clair une fois décodé, ce qui le rend vulnérable à la falsification.

## Étape 4 : Modifier et ré-encoder le cookie

1. Modifier l'objet JSON pour changer le champ `role` de `guest` à `admin` :

```json
{"user_id":1,"username":"guest","role":"admin","email":"guest@techcorp.local"}
```

2. Ré-encoder le JSON modifié en Base64 :

```bash
echo '{"user_id":1,"username":"guest","role":"admin","email":"guest@techcorp.local"}' | base64
```

3. La nouvelle valeur encodée sera :

```
eyJ1c2VyX2lkIjoxLCJ1c2VybmFtZSI6Imd1ZXN0Iiwicm9sZSI6ImFkbWluIiwiZW1haWwiOiJndWVzdEB0ZWNoY29ycC5sb2NhbCJ9
```

## Étape 5 : Remplacer le cookie et recharger la page

Retourner dans les outils de développement, section Cookies.

- Modifier le cookie `user_session` en collant la nouvelle valeur encodée en Base64.
- Enregistrer les modifications et recharger la page (F5).
- Le tableau de bord doit maintenant refléter les privilèges administrateur, donnant accès à des fonctionnalités supplémentaires et révélant le flag.

**Félicitations, vous avez obtenu votre flag !**
