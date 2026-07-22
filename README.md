# Guide des Mariés — template

Guide photo remis aux couples avant leur mariage, par **Matin Shakurov**, photographe.

Ce dépôt contient le **template maître**. Chaque mariage part d'une copie de ce
template, ajustée au planning du couple et au périmètre de la prestation.

## Aperçu local

Le runtime charge ses composants via `fetch()`, qui refuse le protocole `file://` :
ouvrir le `.dc.html` par double-clic laisse les emplacements photo vides. Il faut
servir le dossier en HTTP.

```bash
node serve.js
```

Puis ouvrir http://localhost:8080/

## Structure

```
Guide des Maries.dc.html   template + logique du composant
index.html                 point d'entrée (redirige vers le guide)
serve.js                   serveur d'aperçu local, non utilisé en production
assets/
  css/guide.css            styles
  images/                  photos du guide
  vendor/                  runtime, composant image-slot, React
docs/superpowers/specs/    spécification de la révision de contenu
```

## Hébergement

Le site est statique : HTML, CSS et JS côté client uniquement, aucune action
serveur. Il fonctionne tel quel sur GitHub Pages.

Deux points en dépendent :

- **`.nojekyll`** — sans ce fichier, Jekyll ignore les fichiers commençant par un
  point, dont le sidecar `.image-slots.state.json` où `image-slot` enregistre les
  recadrages.
- **Chemins relatifs** — toutes les ressources sont référencées en `./assets/…`,
  pour fonctionner sous le sous-dossier `/guide_ultime_template/` d'un site de projet.

React est servi depuis `assets/vendor/` plutôt que depuis unpkg.com : le guide ne
dépend d'aucun CDN pour s'afficher. Les fichiers correspondent aux empreintes SRI
attendues par le runtime.

## Notes

- La propriété `slotFill` (panneau Style) vaut `blanc` : les emplacements photo
  encore vides sont rendus en aplat blanc. Un emplacement qui porte un `src`
  s'affiche normalement — aucun réglage à changer en ajoutant des photos.
- Les images sont à servir en 2560 px de large maximum, ré-encodées en sRGB.
