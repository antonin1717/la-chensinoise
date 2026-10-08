# La Chensinoise — site de la course

Site d'une page pour **La Chensinoise**, 10 km de Chens-sur-Léman (Haute-Savoie).
Rendez-vous le dimanche 23 mai 2027 à 11 h 00.

## Contenu

| Fichier | Rôle |
|---|---|
| `index.html` | La page (structure + images intégrées en base64 + trace GPX embarquée). |
| `styles.css` | Toute la mise en forme, y compris les règles `@font-face`. |
| `app.js` | Tout le JavaScript, à commencer par le bloc `CONFIG` (liens, date…). |
| `fonts/` | Polices Gabarito et Nunito hébergées localement (sous-ensemble latin, 253 Ko). |
| `la-chensinoise-10km.gpx` | Trace GPX du parcours (copie de secours ; la trace est aussi embarquée dans `index.html`). |
| `outils/make-gpx.ps1` | Régénère la trace GPX depuis Openrunner et met à jour la copie embarquée dans `index.html`. |
| `Logo/` | Logo au format SVG. |
| `vercel.json` | En-têtes de sécurité appliqués par Vercel (CSP, HSTS, anti-clickjacking…). |

## Modifier le site

Toutes les infos qui changent souvent sont regroupées au début de `app.js`, dans le bloc `CONFIG` :
lien d'inscription Njuko, lien Openrunner, e-mail de contact, date de la course, liens Google Maps, logos du bandeau partenaires.

**Les fichiers vont ensemble** : `index.html` seul ne s'affiche pas correctement, il lui faut `styles.css`,
`app.js` et le dossier `fonts/` à côté de lui.

Après une modification du tracé sur Openrunner :

```powershell
powershell -NoProfile -ExecutionPolicy Bypass -File "outils\make-gpx.ps1"
```

## Mise en ligne

Le site est hébergé sur Vercel, connecté à ce dépôt GitHub : chaque modification envoyée sur la branche `main`
(`git push`) est publiée automatiquement en une minute environ.

## Sécurité

Site **100 % statique** : pas de serveur applicatif, pas de base de données, pas de comptes utilisateurs,
pas de formulaire, pas d'upload, pas de paiement, pas de cookie déposé par le site, aucune dépendance npm.
Aucun secret n'est nécessaire — donc aucun fichier `.env` dans ce dépôt.

Les en-têtes de sécurité (CSP, HSTS, `nosniff`, `X-Frame-Options`, `Referrer-Policy`, `Permissions-Policy`,
COOP/CORP) sont définis dans `vercel.json` et appliqués à toutes les réponses.

La CSP est stricte : `script-src 'self'` et `style-src 'self'`, **sans `'unsafe-inline'`**. Conséquence à
connaître avant de modifier le site :

* pas de `<style>` ni de `<script>` écrit directement dans `index.html` ;
* pas d'attribut `style="…"` dans le HTML — passer par une classe CSS ;
* en JavaScript, modifier le style avec `element.style.setProperty(…)` (autorisé), jamais en écrivant
  un attribut `style` dans du HTML généré ;
* un script externe (statistiques, widget…) devra être ajouté explicitement à la CSP de `vercel.json`.

Les polices sont hébergées sur le site (dossier `fonts/`) : aucune requête vers Google, donc aucune
adresse IP de visiteur transmise.

Règle à tenir : **ne jamais mettre de clé, mot de passe ou token dans `index.html`** — le fichier est public.
Si une fonctionnalité en exige un (envoi d'e-mails, API payante…), il faut passer par une fonction serveur
Vercel et une variable d'environnement, jamais par le code de la page.

Point restant ouvert côté données personnelles : la carte Openrunner intégrée transmet l'adresse IP des
visiteurs à Openrunner dès l'ouverture de la page.
