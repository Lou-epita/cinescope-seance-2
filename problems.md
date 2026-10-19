## 1. Le déclencheur « Réserver » n'était pas un bouton

**Problème:** L'action était un `<div class="fake-button">` qui ne réagissait
qu'au clic.

**Impact utilisateur:** La réservation était impossible au clavier : le `div`
n'est pas atteignable avec Tab et ne réagit ni à Entrée ni à Espace. Un lecteur
d'écran ne l'annonçait pas comme une action.

**Correction:** Le `div` est remplacé par un `<button type="button">`, avec
`aria-haspopup="dialog"`.

**Vérification:**
- Au clavier, le bouton « Réserver Après l'aube » est atteint avec Tab et
  s'ouvre avec Entrée comme avec Espace.
- Panneau Accessibilité des DevTools : rôle `button`.

## 2. La fenêtre n'avait ni rôle, ni nom, ni gestion du focus

**Problème:** La fenêtre était un simple `<div class="modal">`. Le focus
restait sur la page, Échap n'avait aucun effet et la page derrière restait
dans le parcours au clavier.

**Impact utilisateur:** Rien n'indiquait qu'une fenêtre venait de s'ouvrir.
Au clavier, on continuait de parcourir les films cachés derrière le fond
sombre, sans pouvoir atteindre la fenêtre facilement ni la fermer.

**Correction:**
- La fenêtre utilise `<dialog>` avec `showModal()`. Le navigateur lui donne le
  rôle `dialog`, place le focus dans la fenêtre à l'ouverture, la ferme avec
  Échap et rend inactif le contenu derrière.
- Son nom vient du titre `h2`, relié par `aria-labelledby="booking-title"`.

**Vérification:**
- Panneau Accessibilité : rôle `dialog`.
- À l'ouverture, `document.activeElement` est dans le dialogue.
- Tab ne parcourt que les commandes de la fenêtre (Fermer, Revenir aux films) ;
  les films derrière ne sont plus atteints.
- Échap ferme la fenêtre.

## 3. Le bouton de fermeture n'était pas utilisable et le focus était perdu après la fermeture

**Problème:** La croix était un `<span>×</span>` sans nom accessible. Après la
fermeture, le focus n'était rendu à aucun élément.

**Impact utilisateur:**
- Impossible de fermer la fenêtre au clavier.
- Un lecteur d'écran lisait au mieux « multiplication ».
- Après la fermeture, l'utilisateur était renvoyé au début de la page et
  devait retrouver le film qu'il venait de consulter.

**Correction:**
- La croix est un `<button aria-label="Fermer">`, et le « × » est masqué aux
  lecteurs d'écran avec `aria-hidden`.
- Tous les moyens de fermeture (Échap, Fermer, Revenir aux films) déclenchent
  l'événement `close` du dialogue.
- À cet événement, le focus revient au bouton « Réserver » du film ouvert. Ce
  bouton est retrouvé grâce à l'identifiant du film, et non à sa position dans
  la liste.

**Vérification:**
- Panneau Accessibilité : rôle `button`, nom « Fermer ».
- Scénario réalisé avec « Après l'aube », puis avec « Orbite 9 ». Pour chacun,
  on ferme une fois avec Échap, une fois avec le bouton Fermer, et on vérifie à
  chaque fois que le focus revient sur le bouton « Réserver » du bon film :
  - par l'indicateur de focus visible ;
  - par `document.activeElement` dans la console.
