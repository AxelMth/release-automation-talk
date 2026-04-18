# 🚀 Talk — Automatisation des MEPs (V2)

Version corrigée du squelette Slidev. **~25 slides · ~32 min + Q&A**.

## 🔧 Corrections apportées vs V1

- ✅ **Slide 1 propre** (plus de slide vide ni "undefined" dans le plan)
- ✅ **Plus de HTML brut affiché** (espaces préservés autour des blocs HTML)
- ✅ **Slides qui ne débordent plus** (Mermaid backend réduit, contenu allégé)
- ✅ **Pas de mix `<ul>` + listes Markdown** (tout en Markdown pur dans les containers HTML)

## 🏃 Start

```bash
pnpm install
pnpm dev
```

Ouvrir http://localhost:3030

## 🎮 Navigation Slidev

- **`→` / `Space`** : slide suivante (ou déclenche le prochain `v-click`)
- **`←`** : slide précédente
- **`o`** : **fermer/ouvrir l'overview** (le panel de droite que tu vois dans tes screens)
- **`f`** : fullscreen
- **`d`** : mode dark
- **`g`** : aller à une slide précise (tape le numéro)

Si tu veux que le panel de droite soit **fermé par défaut**, c'est normalement le cas quand tu n'as pas cliqué sur le bouton overview. Appuie sur `o` pour le toggle.

## 📐 Les 3 règles Slidev que la V2 respecte

Si tu édites toi-même les slides, garde ces 3 règles en tête :

### Règle 1 — Ligne vide obligatoire dans un bloc HTML

```markdown
<div class="grid">

- item 1 ← la ligne vide au-dessus est cruciale
- item 2

</div>
```

### Règle 2 — Pas de mix `<ul><li>` + Markdown

Utilise des `<div>` comme containers, et du **Markdown pur** (`-`, `**bold**`) à l'intérieur avec des lignes vides.

### Règle 3 — Un titre `#` ou un layout qui gère le vide

Soit chaque slide commence par `# Titre`, soit elle a un frontmatter `---\nlayout: center\n---`.

## 📝 TODO avant le jour J

### Contenu
- [x] Vrais chiffres dans la slide "18 mois plus tard" ({X})
- [ ] 1 verbatim restant (lead tech)
- [ ] `qr-feedback.png` (QR réel vers formulaire)
- [ ] Photos équipe (optionnel) pour les verbatims

### Package
- [ ] Publier `@{handle}/meta` sur npm (alpha)
- [ ] Repo `@{handle}/meta-examples` public avec les 2 exemples
- [ ] Remplacer tous les `{handle}` et `{date}` dans slides.md

### Sur place
- [ ] QR testé depuis 5m avec 3 téléphones
- [ ] Répétition chronométrée
- [ ] PDF backup sur clé USB (`pnpm export`)

## 📂 Structure

```
.
├── slides.md                  # Les 25 slides
├── public/                    # Assets (QR, screenshots, photos)
│   └── README.md              # Checklist des fichiers
├── snippets/
│   └── backend-flow-full.md   # YAML complet pour Q&A
├── package.json
└── .gitignore
```