# DynamicHtml CV Template

Ce projet contient un template HTML/CSS robuste conçu pour la génération de CV au format PDF via **Puppeteer** et **C#**. Il intègre un système d'auto-ajustement intelligent pour garantir que le contenu tient toujours sur une seule page A4.

## 🏗️ Structure du Layout

- **Format Fixe A4** : Le conteneur `.page` est fixé à `210mm x 297mm`.
- **Débordement** : `overflow: hidden` est activé pour garantir un rendu propre.
- **Colonnes (Flexbox)** : 
    - `sidebar` (gauche) : largeur contrôlée par `--cv-sidebar-width`.
    - `main-content` (droite) : prend l'espace restant.
- **Subdivision Verticale** : Chaque zone (`sidebar`/`main`) est un conteneur flex vertical (`column`).
    - `.cv-section` : Utilise `flex: 1` pour se partager l'espace vertical.
    - `.spacer-fixed` : Espace fixe (ex: 20px).
    - `.spacer-dynamic` : Espace proportionnel (ex: 10% de la hauteur).

## 🎨 Conventions de Nommage CSS

Toutes les variables globales utilisent le préfixe `--cv-`.

### Variables Globales (Root)
| Variable | Description |
| :--- | :--- |
| `--cv-sidebar-width` | Largeur de la colonne de gauche (ex: 25%). |
| `--cv-fit-factor` | **Variable pilote (0 à 1)**. Gérée par le JS pour ajuster l'échelle globale. |
| `--cv-min-font-size` / `max` | Bornes de la taille de police (ajustement via `calc`). |
| `--cv-min-line-height` / `max` | Bornes de l'interlignage. |
| `--cv-min-padding` / `max` | Bornes du padding des colonnes. |
| `--cv-min-item-margin` / `max` | Bornes des marges entre les blocs d'expérience. |

### Variables Locales de Section
Chaque bloc `.cv-section` possède des variables surchargeables :
- `--section-padding`, `--section-bg`, `--section-title-color`.

## 🤖 Système d'Auto-Ajustement (Recherche Binaire)

Le script `CV_App` utilise un algorithme de **Recherche Binaire (Logarithmique)** pour trouver instantanément la mise en page optimale.

### Fonctionnement
Contrairement à une réduction pas-à-pas, le script teste le "milieu" des possibles entre le compactage maximum (`factor: 0`) et l'aération maximum (`factor: 1`).
- **Performance** : Stabilisation en **12 itérations** seulement (contre ~100 auparavant).
- **Shrink & Grow** : 
    - Si le contenu dépasse, il est compacté.
    - Si le contenu est court, il s'agrandit automatiquement pour remplir la page harmonieusement.
- **Cohérence Visuelle** : Toutes les dimensions (font-size, padding, margins) évoluent proportionnellement grâce à la variable `--cv-fit-factor` utilisée dans les `calc()` CSS.

### Protection du Layout
- **Pas de coupures** : L'utilisation de `break-inside: avoid` sur les blocs `.exp-item` empêche qu'une expérience soit scindée en bas de page.
- **Ressorts Flex** : Les `.spacer-dynamic` utilisent `flex: 1` pour absorber l'espace vide restant de manière fluide.
- **Icônes & Photos** : Utilisent l'unité `em` pour rester proportionnels au texte lors du changement d'échelle.

### Événements JS
| Événement | Description |
| :--- | :--- |
| `cv:fit-complete` | Déclenché avec le `factor` final trouvé. |
| `cv:limit-reached` | Déclenché si `factor: 0` ne suffit pas à éviter le débordement. |
| `cv:content-updated` | À déclencher après injection de données dynamiques. |
| Événement | Description |
| :--- | :--- |
| `cv:fit-complete` | Déclenché lorsque l'ajustement est terminé et stabilisé. |
| `cv:limit-reached` | Déclenché si le contenu dépasse encore malgré les limites minimales atteintes. |
| `cv:content-updated` | **À déclencher manuellement** après avoir injecté des données dynamiques. |

## 🚀 Utilisation avec Puppeteer (C#)

Voici la marche à suivre recommandée pour votre code C# :

1. **Charger le HTML** et injecter vos données.
2. **Déclencher l'ajustement** :
   ```csharp
   await page.EvaluateAsync("CV_App.adjustContent()");
   ```
3. **Attendre la stabilisation** :
   ```csharp
   await page.WaitForFunctionAsync("window.isFitComplete === true");
   // Note: Vous pouvez ajouter `window.isFitComplete = true` dans l'event listener de `cv:fit-complete`.
   ```
4. **Générer le PDF** :
   ```csharp
   await page.PdfAsync(new PdfOptions { Format = PaperFormat.A4, PrintBackground = true });
   ```

## 📝 Notes de Design
- **Polices** : Utilisez `--cv-font-primary` et `--cv-font-secondary`.
- **Couleurs** : Les textes sont scopés par zone (`--cv-sidebar-text-*` et `--cv-main-text-*`) pour garantir le contraste sur le fond jaune/blanc.
- **Positionnement** : Le conteneur `.page` est en `position: relative`, ce qui permet d'ajouter des éléments flottants (photo, icônes) n'importe où avec `position: absolute`.
