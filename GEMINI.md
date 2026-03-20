# État d'avancement - DynamicHtml CV Template

## Objectifs
Créer un template HTML/CSS robuste pour la génération de CV via Puppeteer et C#.

## Réalisations
- [x] Initialisation du fichier `Template.html`.
- [x] Définition d'un conteneur principal à format fixe (A4 : 210mm x 297mm).
- [x] Mise en place d'un layout Flexbox avec deux colonnes (Side et Main).
- [x] Subdivision verticale des zones Side et Main avec `flex-direction: column`.
- [x] Création de la classe `.cv-section` avec `flex: 1` pour un remplissage dynamique de l'espace.
- [x] Implémentation de spacers fixes (`.spacer-fixed`) et dynamiques (`.spacer-dynamic`) via variables CSS.
- [x] **Système d'auto-ajustement JS :**
    - Détection automatique de l'overflow sur la page A4.
    - Réduction itérative du padding et de la taille de police.
    - Gestion de bornes min/max via variables CSS (`--cv-min-*`, `--cv-max-*`).
    - Événements personnalisés (`cv:fit-complete`) pour synchronisation avec Puppeteer.
- [x] Gestion des débordements (`overflow: hidden`) sur le conteneur principal.
- [x] Support pour le positionnement absolu d'éléments (ex: photo, logo).

## Conventions de nommage adoptées
- Préfixe `--cv-` pour les variables CSS globales.
- Auto-ajustement : `--cv-base-*` (valeur actuelle), `--cv-min-*` (limite basse).
- Événements JS : `cv:fit-complete`, `cv:content-updated`.
- Variables locales de section : Utilisation de `--section-*` à l'intérieur de `.cv-section`.
- Classes structurelles : `.cv-container`, `.cv-section`, `.spacer-*`.

## Prochaines étapes
- [ ] Ajouter des sections de contenu structurées (Expérience, Éducation, Compétences).
- [ ] Préparer les emplacements pour l'injection de données Puppeteer (Placeholders).
- [ ] Améliorer le design visuel (typographie, icônes).
