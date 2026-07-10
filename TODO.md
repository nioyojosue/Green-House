# TODO - SEO & Performances

## Étape 1: Analyse rapide
- [x] Repérer les pages existantes dans la racine
- [x] Vérifier meta description/keywords et présence de placeholder
- [x] Repérer les problèmes de H1 (plusieurs `<h1>`) 
- [x] Repérer les initialisations JS non conditionnelles

## Étape 2: SEO - meta description/keywords par page
- [ ] Mettre à jour `meta name="description"` et `meta name="keywords"` dans toutes les pages HTML du dossier racine


## Étape 3: SEO - Titres H1 uniques
- [ ] S’assurer de 1 seul H1 principal par page
- [ ] Remplacer les H1 décoratifs (ex: dans carousel) par h2/h3 ou spans si nécessaire (sans casser le style)

## Étape 4: SEO - Optimiser images (alt + lazy si possible)
- [ ] Remplacer les `alt` génériques ("Image", "Team Image", etc.) par des alt descriptifs
- [ ] Ajouter `loading="lazy"` + `decoding="async"` aux images non critiques

## Étape 5: Performances - réduire chargement/initialisations libs
- [x] Mettre à jour `js/main.js` pour n’initialiser les plugins que si les éléments existent dans la page


## Étape 6: Vérifications
- [ ] Re-lire quelques pages (index/about/service) pour valider H1/meta/alt
- [ ] Vérifier que les composants JS (counterup, sliders, isotope) fonctionnent toujours sur les pages concernées

