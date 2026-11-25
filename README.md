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

## LCA
- Onglet « LCA » (`#lca` = cours, `#lca/fichiers` = fichiers) : cours modifiable façon Word (Poppins 9, photos, tableaux, documents joints 📎) et fichiers en 4 catégories (À faire annales / À faire TD / Fait annales / Fait TD), dossiers, déplacement, commentaire par dossier ou fichier hors dossier.
- Données : IndexedDB (clé `lca`) + `GET/PUT /api/lca` (KV `lca`, version `lcarev`, sauvegarde quotidienne `lcabackup-AAAA-MM-JJ` 30 j), fusion élément par élément. Cours initial : `lca-seed.txt` (Word « LCA cours »), images `lca-seed-img.txt` (`lcaseed1`, `lcaseed2`).
- Fichiers : `PUT/GET/DELETE /api/file/<id>` (KV `file:<id>`, 24 Mo max) ; photos réduites à 2000 px (WebP/JPEG 0,72), autres fichiers en gzip si gain.

## Sauvegarde
- Onglet « Sauvegarde » (`#sauvegarde`, cinquième `<script>`, objet `window.SV`) : fichiers fabriqués dans l'appli, à la demande, puis Télécharger ou Enregistrer / Partager (iPad).
- Excel `.xlsx` : onglet « Calendrier » (même présentation que le classeur « Révisions générales » : semaines, matière + cours par créneau, ✓ fait, retards en rouge, dimanches, révision du soir) et onglet « Items » (tours, passages faits/prévus, dernier/prochain passage, à revoir avant le, résumé oui/non, cours ajoutés à la main).
- Word `.docx` : tous les résumés (par matière, titre « n° — chapitre » au-dessus de chacun, Poppins 8) et cours LCA (Poppins 9). Photos converties en JPEG, tableaux, couleurs et surlignages conservés.
- Avant export : synchro du planning (`EDN.syncNow`), des résumés (`RS.data`) et du LCA (`LCA.data`).

## Commentaire du jour
- Zone de texte libre « Commentaire » à la fin de chaque journée, dans Jour (sous les créneaux) et dans Planning (sous la révision du soir). Même note aux deux endroits.
- Stockée dans l'état du planning (`st.N = {'AAAA-MM-JJ': texte}`), donc synchronisée avec `/api/state`. Enregistrement automatique (0,8 s après la frappe, ou en quittant la zone). Conservée par Annuler, Régénérer et Réinitialiser.
- Export Excel : ligne « 📝 » sous la révision du soir de chaque jour (blocs de 17 lignes par semaine).

## Objectif tour 1
- Tous les 1ers passages (tour 1) sont placés au plus tard à la date « Tous les tours 1 vus au plus tard le » (Réglages, `st.t1`, 31 août par défaut). Les 2ᵉ passages viennent ensuite (et avant seulement s'il reste de la place).
- S'il manque des demi-journées, les fins de matière incomplètes (1-2 cours) sont regroupées par deux dans une demi-journée ; l'autre demi-journée du jour reprend l'une des deux matières (toujours 2 matières par jour).
- Suivi : bandeau qui signale les cours non vus dont le 1er passage tombe après la date.
