---
id: new-013-0000-0000-0000-000000000013
title: Internationalization
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781600000013
---
# Internationalization

**Links**: [[Web Development Fundamentals]] | [[Programming Language Paradigms]] | [[Programming Resources]] | [[Testing Guide]]

## What is i18n?

Internationalization (i18n) is designing software to support multiple languages and regions without code changes. Localization (l10n) is translating content for specific locales.

## Key Concepts

| Term | Meaning | Example |
|------|---------|---------|
| **Locale** | Language + region | `en-US`, `fr-FR`, `ja-JP` |
| **ICU Message Format** | Standard for translations | `{count, plural, one {# item} other {# items}}` |
| **LTR/RTL** | Text direction | Arabic, Hebrew read right-to-left |
| **Plural Rules** | Language-specific plurals | 1 file, 2 files (English) vs 1, 2, 3 (Russian) |

## Python Example (gettext)

```python
import gettext

# Set locale
lang = gettext.translation('messages', localedir='locale', languages=['fr'])
lang.install()

# In code
print(gettext("Hello, world!"))      # French: "Bonjour, le monde !"
print(gettext("You have %d new messages") % count)
```

## JavaScript Example (i18next)

```javascript
import i18next from 'i18next';

i18next.init({
  lng: 'fr',
  resources: {
    fr: {
      translation: {
        "welcome": "Bienvenue",
        "items": "{{count}} élément",
        "items_plural": "{{count}} éléments",
        "context": "I have {{count}} items"
      }
    }
  }
});

i18next.t('welcome');                    // "Bienvenue"
i18next.t('items', {count: 5});          // "5 éléments"
```

## Translation File Structure

```json
// locale/fr/translation.json
{
  "common": {
    "save": "Enregistrer",
    "cancel": "Annuler",
    "delete": "Supprimer"
  },
  "errors": {
    "not_found": "Page non trouvée",
    "server_error": "Erreur du serveur"
  }
}
```

## Best Practices

- Extract all user-facing strings into translation files — never hardcode
- Use message IDs, not translated text, as keys
- Support pluralization and gender rules
- Allow for text expansion (German text can be 30% longer)
- Test with all supported locales during QA

**Next**: [[Event Sourcing]] — Store events, not state