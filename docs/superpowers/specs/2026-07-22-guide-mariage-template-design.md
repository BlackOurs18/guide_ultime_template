# Guide des Mariés — template maître

**Date :** 2026-07-22
**Statut :** validé, prêt pour plan d'implémentation

---

## 1. Contexte

`Guide des Maries.dc.html` est un guide photo remis aux couples avant leur mariage.
Dans sa forme actuelle il couvre 8 sections, toutes centrées sur les moments que le
photographe traite lui-même : préparatifs, first look, photos de couple, photos de
groupe, plus des conseils transverses.

Deux manques motivent cette révision.

**La journée n'est couverte qu'à moitié.** Cérémonies, vin d'honneur, repas, discours
et soirée n'ont aucune section. Le guide répète « selon votre planning » (sections 02
et 04) sans jamais montrer de planning.

**Le fichier n'est pas pensé pour être dupliqué.** Matin Shakurov veut un template
maître exhaustif, copié puis élagué pour chaque mariage. Aujourd'hui, retirer une
section oblige à renuméroter les suivantes à la main et laisse un lien mort dans le
menu.

**Résultat visé :** un template couvrant la journée entière, où adapter un mariage
consiste à retirer des blocs et remplacer des valeurs — jamais à écrire du contenu
neuf.

### Principe directeur

Adapter n'est pas masquer. Un moment que la prestation ne couvre pas garde sa section
et ses conseils, avec une mention explicite. Le couple garde l'information utile et
sait à quoi s'attendre à la livraison.

---

## 2. Contraintes

- **Format `.dc.html`.** Le runtime lit `scriptEl.textContent` (`support.js:27-33`) et
  n'accepte aucun `src` : la logique du composant reste inline. Le CSS, lui, est
  externalisé dans `assets/css/guide.css` — `<helmet>` clone les `<link>` dans le
  `head` (`support.js:1377-1404`).
- **Pas de dépôt git** dans ce projet : la spec n'est pas commitée.
- **Responsive déjà validé** (320 → 1440 px) : les nouvelles sections doivent
  respecter les mêmes règles, sans débordement horizontal.
- **Styles inline** conservés dans le markup (idiome du format). Les motifs répétés
  passent en classes dans `guide.css`.

---

## 3. Conventions du template

Trois mécanismes rendent la duplication rapide. Les deux premiers sont indissociables :
sans génération du menu, l'auto-numérotation laisse des liens morts.

### 3.1 Valeurs à remplacer

Toute donnée variable d'un mariage à l'autre est balisée :

```html
<span class="ms-var">[[ heure d'arrivée ]]</span>
```

Style : fond légèrement teinté de l'accent, impossible à manquer en relecture.
Recensement avant envoi : `grep -o '\[\[[^]]*\]\]' "Guide des Maries.dc.html"`.

Variables prévues : heure d'arrivée · durée des préparatifs · horaires de chaque
ligne de la frise · heure du coucher du soleil · heure de fin de prestation · délai
de préparation de la salle.

### 3.2 Numérotation automatique

Les numéros `01`–`12` disparaissent du markup au profit d'un compteur CSS
(`counter-increment` sur les sections, rendu via `::before`). Le rendu visuel actuel
est conservé : chiffre en contour, `-webkit-text-stroke`.

Retirer une section renumérote automatiquement les suivantes.

### 3.3 Menu généré depuis les sections présentes

Chaque section porte son libellé de menu :

```html
<section id="ceremonies" data-nav="Les cérémonies">
```

Au montage, le composant parcourt `[data-nav]` et construit les liens. Supprimer une
section retire son entrée de menu sans intervention.

Les 9 liens écrits en dur dans `#ms-nav-links` sont supprimés. Seul `#ms-nav-contact`
subsiste dans le markup, et reste en dernière position. Le `<footer id="contact">` ne
porte donc **pas** de `data-nav` : il est déjà atteint par ce lien, et ne doit être ni
numéroté ni dupliqué dans le menu.

### 3.4 Encart « hors prestation »

Bloc réutilisable, posable sur n'importe quelle section :

```html
<div class="ms-hors-presta">
  Ce moment n'est pas couvert par ma prestation —
  voici tout de même l'essentiel pour bien le préparer.
</div>
```

---

## 4. Structure cible

Ordre chronologique réel de la journée. 12 sections.

| N° | Section | État |
|----|---------|------|
| 01 | Le déroulé de votre journée | nouveau |
| 02 | Les préparatifs | révisé |
| 03 | Le first look | révisé |
| 04 | Les cérémonies | nouveau |
| 05 | Les photos de groupe | révisé |
| 06 | Le vin d'honneur | nouveau |
| 07 | Vos photos de couple | révisé |
| 08 | Le repas & les discours | nouveau |
| 09 | La soirée & l'ouverture de bal | nouveau |
| 10 | Être naturels devant l'objectif | inchangé |
| 11 | Votre checklist | révisé |
| 12 | FAQ | révisé |

La section « La cérémonie unplugged » disparaît en tant que section autonome : son
contenu est repris tel quel comme bloc de la section 04.

---

## 5. Contenu section par section

### 01 — Le déroulé de votre journée *(nouveau)*

Ouvre sur le principe qui commande le reste : la lumière ne se négocie pas.

- Frise verticale, une ligne par moment, horaire en `ms-var`
- Encadré **La règle d'or** : la golden hour est le seul créneau imposé par le soleil
  et non par le couple. On la fixe en premier, le planning se cale autour.
- Durées repères : préparatifs `[[ 1h30 ]]` · first look 15 min · groupes 30 min ·
  couple 20 à 45 min
- Mention de la marge : prévoir 15 min de battement par bloc

### 02 — Les préparatifs *(révisé)*

Les 4 blocs existants sont conservés (lumière, pièce rangée, détails réunis, timing).

- « environ une heure et demie » → `ms-var`
- **Nouveau bloc — préparatifs séparés :** deux lieux imposent un temps de trajet à
  budgéter, ou un second photographe. Trou logistique du guide actuel : découvert le
  jour J, il fait sauter le planning.

### 03 — Le first look *(révisé)*

Contenu conservé. Ajout d'une phrase de raccord : avec first look, l'essentiel des
photos de couple est déjà fait, le créneau golden hour retombe à ~20 min.

Lève la contradiction avec la section 07, qui réclame aujourd'hui 45 min sans tenir
compte du first look.

### 04 — Les cérémonies *(nouveau)*

Trois sous-blocs à élaguer selon le couple, deux blocs communs, plus l'unplugged.

**Civile.** 15-20 min, salle souvent petite et sombre, placement imposé. Demander à
l'officiant ce qui est permis. Moments clés : alliances et signature. La sortie sur le
perron offre souvent la meilleure lumière de la séquence.

**Religieuse.** Chaque lieu a ses règles, à vérifier avec le célébrant *en amont*
(déplacements, flash, zones interdites). Travail sans flash pour ne pas rompre le
recueillement.

**Laïque.** La plus libre, donc celle qui se prépare le plus.
**L'orientation d'abord : les mariés dos au soleil, jamais face** — sinon yeux plissés
et visages en contre-jour. Fond dégagé, sans voitures ni tables du traiteur. Ombre
prévue pour les invités en plein après-midi. *À mettre en valeur typographique : c'est
l'erreur la plus coûteuse du guide.*

**Le placement.** Allée centrale libre, couloir visuel préservé.

**La sortie.** Haie d'honneur, pétales ou bulles : désigner qui distribue *avant*,
sortir lentement, dans un endroit dégagé.

**Bloc unplugged.** Contenu actuel de la section 05 repris tel quel, encadré compris.

### 05 — Les photos de groupe *(révisé)*

Fond inchangé. La liste d'exemple passe de 8 à 12 entrées, pour que retirer des lignes
soit l'opération naturelle et que le texte « huit à douze groupes » soit cohérent avec
ce qui est montré.

Quatre entrées ajoutées : oncles & tantes / cousins · collègues · voisins et amis des
parents · les enfants présents.

### 06 — Le vin d'honneur *(nouveau)*

- Gisement des photos spontanées : retrouvailles, rires, embrassades
- Le lieu : ombre ou mi-ombre. Le plein soleil creuse les visages et fait plisser les
  yeux
- Les groupes s'y insèrent — renvoi vers 05
- Discours ou animations : transmettre l'ordre pour un placement anticipé
- Moment probable de l'éclipse golden hour selon la lumière

### 07 — Vos photos de couple *(révisé)*

- « autour de 21 h chez nous » → `[[ coucher du soleil ]] − 1 h`. La formule actuelle
  n'est vraie qu'en juillet-août : coucher du soleil dans le Cher ~21h50 en juin,
  ~20h en septembre, ~18h30 en octobre.
- Raccord first look (voir 03)
- Le bloc statistique « **0** chose à retenir » est remplacé par une ligne en clair :
  un « 0 » en gros chiffre à côté de « 45 » et « −1 h » se lit d'abord comme une durée
  nulle.
- Reste conservé, y compris « prévenez vos témoins »

### 08 — Le repas & les discours *(nouveau)*

**Le décor de salle.** La salle dressée se photographie avant l'entrée des invités.
Donc : salle terminée `[[ 15-20 min ]]` avant l'ouverture, bougies allumées. C'est
l'unique créneau possible ; sinon ces images n'existent pas. *À mettre en valeur : le
conseil le plus rentable du guide.*

**L'entrée des mariés.** Prévenir le DJ ou le traiteur pour un placement anticipé.

**Les discours.** Nombre et ordre transmis en amont ; prévoir un point lumineux si la
salle est sombre.

**Le gâteau.** Si la prestation s'arrête avant, le placer plus tôt dans la soirée.

**Le repas du photographe.** Servi en même temps que les mariés, pour être disponible
dès les premiers discours. Renvoi vers la FAQ.

### 09 — La soirée & l'ouverture de bal *(nouveau)*

- **L'ouverture de bal :** demander au DJ fumée légère et pas de stroboscope sur le
  premier morceau — ils rendent les images inexploitables. Un point lumineux fixe
  suffit.
- **La fin de prestation :** `[[ heure ]]`, énoncée clairement
- **La sortie aux étincelles :** prévoir qui allume, un espace dégagé, l'accord du lieu
- Section de référence pour l'encart « hors prestation » (§3.4)

### 10 — Être naturels devant l'objectif *(inchangé)*

Les 4 blocs fonctionnent. Aucune modification.

### 11 — Votre checklist *(révisé)*

Les 12 items actuels ne couvrent que les préparatifs. Passage à deux groupes.

**À réunir** — les 12 objets actuels. « La robe sur un beau cintre » devient « La tenue
sur un beau cintre » : le template sert tous les couples.

**À caler** — le panneau unplugged · les pétales et qui les distribue · l'ordre des
discours · le repas photographe confirmé auprès du traiteur · le témoin chef
d'orchestre désigné · la salle dressée avant l'entrée des invités · les horaires photo
validés *(déplacé depuis le premier groupe)*.

Techniquement, la checklist reste **un seul tableau indexé**, simplement rendu en deux
groupes titrés. Les index restent continus de 0 à 18 sur l'ensemble, ce qui laisse la
persistance par cookie (`msguide_checklist`) inchangée. Corollaire à assumer : ajouter
ou retirer un item décale les index suivants et invalide une progression déjà
enregistrée sur l'appareil d'un couple. Sans conséquence ici — chaque mariage part
d'une copie neuve du template.

### 12 — FAQ *(révisé)*

Les 6 questions actuelles sont conservées. Six ajouts :

1. **Que deviennent nos photos ?** Droits d'usage, réseaux sociaux, publication sur le
   site du photographe. Seule question absente qui touche au contrat.
2. **Et s'il vous arrive quelque chose ?** Double sauvegarde des fichiers, confrère en
   renfort.
3. **Jusqu'à quelle heure restez-vous ?**
4. **Proposez-vous des albums et des tirages ?**
5. **Un second photographe est-il utile ?**
6. **Comment ça se passe si nous avons aussi un vidéaste ?**

---

## 6. Mise en œuvre technique

### Fichiers touchés

- `Guide des Maries.dc.html` — markup des 12 sections, logique de génération du menu
- `assets/css/guide.css` — classes `ms-var`, `ms-hors-presta`, `ms-timeline`,
  compteur de sections

### Classes CSS à ajouter

| Classe | Rôle |
|--------|------|
| `.ms-var` | valeur à remplacer, fond teinté accent |
| `.ms-hors-presta` | encart « non couvert par ma prestation » |
| `.ms-timeline` | frise horaire de la section 01 |
| `.ms-section-num` | numéro auto via compteur CSS |
| `.ms-highlight` | mise en valeur des deux conseils clés (§04 laïque, §08 salle) |

### Génération du menu

Dans `componentDidMount`, avant l'`IntersectionObserver` : parcourir
`document.querySelectorAll('[data-nav]')`, construire un lien par entrée dans
`#ms-nav-links`, conserver `#ms-nav-contact` en dernier. Les liens générés héritent des
styles existants et du comportement de fermeture du menu mobile.

### Compteur de sections

```css
body { counter-reset: ms-section; }
section[data-nav] { counter-increment: ms-section; }
.ms-section-num::before { content: counter(ms-section, decimal-leading-zero); }
```

`decimal-leading-zero` reproduit le format `01`, `02` actuel.

Le compteur ne s'incrémente que sur `section[data-nav]`. Sont donc exclus, comme
aujourd'hui : le `<header id="top">`, la section d'intro `#ms-intro` (« Le mot du
photographe ») et le `<footer id="contact">` — aucun des trois n'est numéroté.

---

## 7. Hors périmètre

- Dé-inliner les ~200 attributs `style=""` du markup en classes. Chantier séparé.
- Mécanisme de personnalisation par propriétés d'éditeur : écarté, le workflow retenu
  est la duplication du fichier.
- Photos réelles : les emplacements restent en aplat blanc (`slotFill: blanc`) jusqu'à
  ce que Matin les remplisse.

---

## 8. Vérification

1. **Responsive non régressé** — rejouer le harnais Playwright à 320 / 390 / 820 /
   1024 / 1440 px : débordement horizontal nul, burger cliquable, fermeture du menu au
   tap extérieur, sections à la largeur exacte du viewport.
2. **Numérotation** — retirer une section au hasard, vérifier que les suivantes se
   renumérotent et que le menu perd l'entrée correspondante.
3. **Menu généré** — les 12 entrées présentes, dans l'ordre, `Contact` en dernier ;
   chaque lien atteint sa section.
4. **Variables** — `grep -o '\[\[[^]]*\]\]'` liste toutes les valeurs à remplacer,
   aucune oubliée hors de `ms-var`.
5. **Encart hors prestation** — posé sur la section 09, s'affiche correctement sur
   mobile et desktop.
6. **Checklist** — les deux groupes cochables, persistance cookie conservée après
   rechargement.
7. **Aucune erreur JS** dans la console aux 5 largeurs.
