# Guide d'installation — Colis (Suivi automatique)

## Architecture

```
Ton site (GitHub Pages)
    → appelle ton Cloudflare Worker
        → qui appelle Ship24 avec ta clé secrète
            → Ship24 récupère le suivi chez le transporteur
        ← renvoie les résultats
    ← affiche les détails dans l'app
```

---

## Étape 1 : Créer un compte Ship24 (gratuit)

1. Va sur **https://www.ship24.com**
2. Clique "Sign Up" → crée ton compte
3. Dans le dashboard, choisis le plan **"Per-call" gratuit** (100 requêtes gratuites)
4. Va dans **API Keys** → copie ta clé API (elle ressemble à `apik_xxxxxxxxxxxx`)
5. **Garde cette clé secrète**, ne la mets jamais dans ton code public

---

## Étape 2 : Créer le Cloudflare Worker (gratuit)

1. Va sur **https://dash.cloudflare.com** → crée un compte si besoin
2. Dans le menu à gauche, clique sur **Workers & Pages**
3. Clique **Create** → **Create Worker**
4. Donne-lui un nom, par exemple : `colis-api`
5. **Supprime le code par défaut** et **colle tout le contenu du fichier `worker.js`**
6. Clique **Deploy**

### Configurer les variables secrètes :

7. Après le déploiement, va dans **Settings** → **Variables and Secrets**
8. Ajoute deux variables :

   | Nom de la variable | Valeur |
   |---|---|
   | `SHIP24_API_KEY` | `apik_ta_cle_ici` (ta clé Ship24) |
   | `ALLOWED_ORIGIN` | `https://drayk0.github.io` |

   ⚠️ Pour SHIP24_API_KEY, clique sur **Encrypt** pour la protéger.

9. Note l'URL de ton worker, elle est en haut de la page :
   **`https://colis-api.drayk0.workers.dev`** (ou similaire)

---

## Étape 3 : Mettre à jour ton site GitHub

1. Va sur ton repo **github.com/Drayk0/Colis**
2. Clique sur `index.html` → icône crayon (Edit)
3. **Remplace tout le contenu** par le nouveau fichier `index.html`
4. Commit les changements

---

## Étape 4 : Connecter l'API dans l'app

1. Ouvre ton site : **https://drayk0.github.io/Colis/**
2. Clique sur la bannière jaune **"API non configurée"** (ou l'icône ⚙️ en haut)
3. Colle l'URL de ton Cloudflare Worker :
   ```
   https://colis-api.drayk0.workers.dev
   ```
4. Clique **Enregistrer**
5. La bannière passe en vert : **"API connectée"** ✅

---

## Utilisation

### Suivi automatique
- Quand tu ajoutes un colis avec un numéro de suivi → l'app appelle automatiquement l'API
- Le **transporteur** est détecté automatiquement par Ship24
- Le **statut** est mis à jour (commandé → expédié → transit → livré)
- Les **étapes détaillées** sont importées (centre de tri, en livraison, etc.)

### Bouton ↻ (Synchroniser)
- Sur chaque carte, clique sur ↻ pour rafraîchir les infos
- Les nouvelles étapes sont ajoutées à la timeline
- Le statut et la date de livraison estimée sont mis à jour

### Bouton ↗ (Suivre)
- Ouvre directement la page de suivi du transporteur

### Mode hors-ligne
- L'app fonctionne toujours même sans l'API
- Tu peux ajouter des étapes manuellement
- La détection du transporteur par numéro fonctionne en local

---

## Coûts

| Service | Gratuit | Limite |
|---|---|---|
| GitHub Pages | ✅ | Illimité |
| Cloudflare Workers | ✅ | 100 000 requêtes/jour |
| Ship24 Per-call | ✅ | 100 requêtes gratuites |
| Ship24 après 100 | ~0.06€/requête | Selon plan |

Pour un usage personnel (quelques colis par mois), **tout est 100% gratuit**.

---

## Dépannage

| Problème | Solution |
|---|---|
| "Erreur 500" | Vérifie ta clé Ship24 dans les variables du Worker |
| "Aucune info trouvée" | Le colis n'est peut-être pas encore scanné par le transporteur |
| CORS error | Vérifie que `ALLOWED_ORIGIN` = ton URL GitHub Pages exacte |
| La bannière reste jaune | Vérifie l'URL du Worker dans les paramètres de l'app |
