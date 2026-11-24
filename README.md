# Révisions EDN (CAP EDN)

Appli de révision EDN, publiée sur https://revisions-edn.nicolas-segond.workers.dev/

- `worker.js` : tout le site (page + API de synchro `/api/state`)
- `wrangler.toml` : réglages Cloudflare (stockage KV `DATA`)
- Chaque envoi sur la branche `main` est publié automatiquement par Cloudflare (Workers Builds).

## Modifier l'appli
Modifier `worker.js` (le HTML est une chaîne JSON sur la ligne `const HTML = ...`), puis `git push` sur `main`. Cloudflare reconstruit et publie en 1 à 2 minutes.

## Flashcards
- Onglet « Flashcards » : données séparées du planning (clé KV `fc`, sauvegarde quotidienne `fcbackup-AAAA-MM-JJ` gardée 30 jours), fusion carte par carte entre appareils.
- Images : compressées dans l'appli (1280 px max, WebP/JPEG qualité 0,7) puis stockées une par une (`img:<id>`, API `/api/img/<id>`).

## Résumés de cours
- Onglet « Résumés » (`#resumes`) : sommaire par matière, page par chapitre (clé = n° de l'item), vue matière et vue générale façon document Word, police Poppins 8.
- Données : IndexedDB locale (clé `rs`) + `GET/PUT /api/rs` (document JSON stocké tel quel dans la clé KV `rs`, version dans `rsrev`, sauvegarde quotidienne `rsbackup-AAAA-MM-JJ` 30 jours). Fusion chapitre par chapitre dans l'appli (le plus récent gagne) ; `PUT` refusé (409) si la version de base n'est plus la bonne.
- Contenu initial : `resumes-seed.txt` (résumés importés du Word « Résumé EDN2027 », texte et surlignages inchangés) servi tant que rien n'a été modifié ; images initiales dans `resumes-seed-img.txt` (`rsseed01`…).
- Accès depuis les créneaux (Jour, Planning), la liste des Items et leurs fiches : bouton livre.
