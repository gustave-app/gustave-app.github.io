# Site public des liens d'ami (`gustave-app.github.io`)

Le code de l'app reste privé. Seul ce dossier `site/` est copié dans le dépôt **public**
`gustave-app/gustave-app.github.io`, servi gratuitement par GitHub Pages à l'adresse `https://gustave-app.github.io/`.

```
site/
├── .nojekyll                               fichier vide : sans lui, GitHub ignore .well-known
├── .well-known/assetlinks.json             Android : prouve que le domaine connaît l'app
├── .well-known/apple-app-site-association  iOS : idem (sans extension)
└── ami/index.html                          la page ouverte quand l'app n'est pas installée
```

Un lien d'ami ressemble à `https://gustave-app.github.io/ami/?f=603&t=Matrix&de=Léa`.

- **App installée** : Android (App Links) et iOS (Universal Links) l'ouvrent directement dans Gustave,
  qui range le film dans « Conseillés par mes amis ». Rien à copier ni à coller.
- **App pas installée** : la page `ami/index.html` s'affiche. Elle envoie vers Google Play ou l'App Store
  dès que `PLAY_STORE_URL` / `APP_STORE_URL` sont renseignées en haut de son script. En attendant, elle
  montre le film et annonce l'app.

## Mise en place

1. Sur GitHub, dans l'organisation `gustave-app`, créer le dépôt **public** `gustave-app.github.io` (exactement ce nom).
2. Y copier le contenu de `site/` (avec `.nojekyll` et `.well-known`).
3. Settings > Pages : Source **Deploy from a branch**, branche **main**, dossier **/ (root)**.
4. Vérifier `https://gustave-app.github.io/ami/?f=603&t=Matrix&de=Paul` et
   `https://gustave-app.github.io/.well-known/assetlinks.json`.

## Android : l'empreinte de la clé

Remplacer la ligne `REMPLACER:…` de `.well-known/assetlinks.json` par l'empreinte SHA-256 de la clé
qui signe l'app :

- en développement : `cd android && ./gradlew signingReport`, ligne `SHA-256` de la variante `debug`
  (ou `keytool -list -v -keystore ~/.android/debug.keystore -alias androiddebugkey -storepass android`) ;
- sur Google Play : Play Console > Test et publication > Intégrité de l'application > Signature d'application.

On peut mettre plusieurs empreintes dans la liste (debug et Play). Après installation de l'app :
`adb shell pm get-app-links com.cinesuggest.cine_suggest` doit afficher `verified` pour `gustave-app.github.io`.

## iOS (compte Apple Developer payant)

1. Remplacer `TEAMID` dans `.well-known/apple-app-site-association` par l'identifiant d'équipe
   (developer.apple.com > Account > Membership details > Team ID).
2. Xcode : Runner > Signing & Capabilities > + Capability > Associated Domains,
   ajouter `applinks:gustave-app.github.io`.

## Autre domaine

Changer `FRIEND_LINK_BASE` (`--dart-define`), l'`intent-filter` d'`AndroidManifest.xml` et le domaine
associé dans Xcode.
