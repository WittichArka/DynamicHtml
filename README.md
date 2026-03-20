# DynamicHtml CV Template

Ce projet propose un moteur de rendu de CV HTML/CSS hautement adaptable, optimisé pour la génération de PDF via **Puppeteer** et **C#**. Il garantit une mise en page parfaite sur une seule page A4 grâce à un système d'auto-ajustement intelligent.

## 🌟 Nouveautés (v2.0)

- **Design "Pills" Réactif** : Les titres de sections utilisent désormais le composant `.section-pill`, qui s'adapte dynamiquement en taille et en espacement.
- **Unités Proportionnelles (`em`)** : Icônes, rayons de bordure et paddings internes sont désormais liés à la taille de la police globale.
- **Distribution Verticale Totale** : Utilisation de `spacer-dynamic` dans la sidebar et le contenu principal pour un équilibre visuel parfait.
- **Optimisation Print de Haut Niveau** : Support natif de `-webkit-print-color-adjust: exact` pour garantir le rendu des dégradés et des fonds colorés.
- **Gestion des Calques Graphiques** : Support intégré pour les images de fond (`board-bg`) et les bordures de photo complexes (`gradient-border-wrapper`).

## 🏗️ Architecture du Template

### Fichiers de Référence
- `Template.html` : La version standard, neutre et prête pour l'injection de données (`{{Placeholders}}`).
- `large2.html` : Un exemple de design riche (inspiré de `cv.html`) utilisant toutes les fonctionnalités dynamiques.

### Structure Flexbox
- **Format Fixe A4** : Conteneur `.page` (210mm x 297mm) avec `overflow: hidden`.
- **Colonnes** : `sidebar` (gauche) et `main-content` (droite).
- **Subdivision Verticale** : 
    - `.cv-section` : Blocs de contenu atomiques.
    - `.spacer-fixed` : Espacements de sécurité (ex: entre Header et Expériences).
    - `.spacer-dynamic` : Ressorts flex (`flex: 1`) qui absorbent le vide pour "tendre" le layout sur toute la hauteur.

## 🎨 Système de Variables CSS (Design Tokens)

Toutes les dimensions critiques sont calculées dynamiquement à partir d'un **Fit Factor (0 à 1)**.

| Variable | Usage | Comportement Dynamique |
| :--- | :--- | :--- |
| `--cv-fit-factor` | **Pilote** | Modifié par le JS (Recherche Binaire). |
| `--cv-font-size` | Police | Évolue entre `min` (10px) et `max` (16px). |
| `--cv-padding` | Espacement | Définit l'aération des colonnes et des composants. |
| `--cv-item-margin` | Marges | Contrôle l'espace entre les sections et les items. |

### Redimensionnement Proportionnel
Pour garantir la cohérence visuelle, nous utilisons des unités relatives :
- **Icônes & Bordures** : Définies en `em` pour suivre la taille du texte.
- **Paddings Internes** : Calculés via `calc(var(--cv-padding) * ratio)`.

## 🤖 Moteur d'Auto-Ajustement (Recherche Binaire)

Le script `CV_App` stabilise la mise en page en **12 itérations** (Précision < 0.1%).

1. **Test** : Le script applique un facteur (ex: 0.5).
2. **Détection** : Il vérifie si `scrollHeight > offsetHeight` (overflow).
3. **Ajustement** : Il affine la fourchette de recherche jusqu'à trouver le facteur maximum permettant de tout faire tenir sur une page.

### Événements JS pour Puppeteer
| Événement | Description |
| :--- | :--- |
| `cv:fit-complete` | L'ajustement est terminé (prêt pour `page.pdf()`). |
| `cv:content-updated` | Déclencher après avoir injecté vos données via JS. |
| `cv:limit-reached` | Alerte si le contenu est trop volumineux pour tenir même au minimum. |

## 🚀 Recommandations pour Puppeteer (C#)

```csharp
// 1. Injecter les données
await page.EvaluateAsync("document.getElementById('nom').innerText = 'Arka Wittich'");

// 2. Lancer l'ajustement
await page.EvaluateAsync("CV_App.adjustContent()");

// 3. Attendre l'événement de fin
// Il est conseillé d'écouter 'cv:fit-complete' côté JS pour positionner un flag
await page.WaitForFunctionAsync("window.cvReady === true");

// 4. Exporter
await page.PdfAsync(new PdfOptions { 
    PrintBackground = true, 
    Format = PaperFormat.A4,
    MarginOptions = new MarginOptions { Top = "0", Right = "0", Bottom = "0", Left = "0" }
});
```

## 📝 Conventions de Design
- **Pas de coupures** : Utilisez `break-inside: avoid;` sur les items critiques.
- **Images** : Préférez le format **Base64** pour éviter les problèmes de chargement réseau lors de la génération PDF.
- **Couleurs** : Utilisez `-webkit-print-color-adjust: exact` sur les éléments ayant des fonds sombres ou des dégradés.
