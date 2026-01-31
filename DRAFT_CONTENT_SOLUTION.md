# Solution pour cacher du contenu avec le frontmatter draft

## Implémentation actuelle

Le système de filtrage de contenu utilise maintenant la propriété `draft` dans le frontmatter pour cacher des pages et leurs sous-chapitres du menu de navigation.

### Comment cacher des pages

Pour cacher une page ou un chapitre entier du menu de navigation, ajoutez simplement `draft: true` dans le frontmatter du fichier markdown:

```yaml
---
ogImage: true
draft: true
---
```

### Exemple: Chapitres cachés

Les pages "Nuxt Concepts" (02.concepts) sont actuellement cachées en utilisant cette méthode.

## Solution pour cacher les "slides" à partir de la partie 3

Pour cacher du contenu à partir d'une partie 3 (ou toute autre section future):

### Option 1: Utiliser le frontmatter draft (Recommandé)

1. Créer la structure de la partie 3 dans `/content/en/03.nom-de-la-partie/`
2. Ajouter `draft: true` dans le frontmatter de chaque fichier `index.md` que vous souhaitez cacher
3. Le menu de navigation filtrera automatiquement ces pages

Exemple de structure:

```
content/
  en/
    03.partie-3/
      01.index.md         # draft: true
      02.chapitre-1/
        index.md          # draft: true
      03.chapitre-2/
        index.md          # draft: true
```

### Option 2: Utiliser le frontmatter unlisted

Si vous voulez que le contenu soit accessible par URL directe mais pas visible dans le menu:

```yaml
---
unlisted: true
---
```

### Option 3: Exclusion au niveau de content.config.ts

Pour exclure complètement une section du système de contenu:

Modifier `/content.config.ts`:

```typescript
export default defineContentConfig({
  collections: {
    en: defineCollection({
      type: 'page',
      source: {
        include: 'en/**',
        exclude: ['**/.template/**', '**/03.*/**'], // Exclut tous les dossiers commençant par 03.
      },
    }),
  },
})
```

## Composant responsable

Le filtrage est géré par `/components/ContentNavItem.vue` qui vérifie les propriétés:

- `draft`: Cache complètement le contenu
- `unlisted`: Cache du menu (sauf si on est déjà sur la page)

```vue
<div v-if="resolved && (!(resolved.meta as any)?.unlisted || current?.startsWith(resolved.path)) && !(resolved.meta as any)?.draft">
```
