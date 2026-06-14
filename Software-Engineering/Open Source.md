---
id: a1b2c3d4-1035-4000-8000-000000000035
title: Open Source
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781500001035
---
# Open Source

Open source software (OSS) is software with publicly accessible source code that anyone can inspect, modify, and distribute.

## Open Source Licenses

| License | Type | Requirements |
|---------|------|-------------|
| MIT | Permissive | Include copyright notice |
| Apache 2.0 | Permissive | Include notice, state changes |
| BSD (2/3-clause) | Permissive | Include notice, no endorsement |
| GPL v2 | Copyleft | Distribute source with modifications |
| GPL v3 | Copyleft | Also covers patents, TPMs |
| AGPL | Strong copyleft | Apply over network too |
| LGPL | Weak copyleft | Linkable from proprietary |
| MPL 2.0 | File-level copyleft | Share changes to modified files |

## Choosing a License

```
Want broad adoption? → MIT or Apache 2.0
Want changes shared? → GPL family
Part of a larger org? → Apache 2.0 (patent protection)
Library for proprietary? → MIT, Apache, LGPL
```

## Contribution Workflow

```
Fork → Clone → Branch → Commit → Push → PR → Review → Merge
```

## Good First Contributions

| Area | Examples |
|------|----------|
| Documentation | Fix typos, improve examples |
| Tests | Add test coverage |
| Bugs | Good-first-issue labels |
| Localization | Translate strings |
| Refactoring | Improve code quality |

## PR Etiquette

- Keep PRs small and focused
- Write descriptive title and body
- Link to related issue
- Respond to review feedback promptly
- Squash commits before merge
- Ensure CI passes

## Open Source Governance

| Model | Examples | Description |
|-------|----------|-------------|
| BDFL | Linux (Linus), Python (Guido) | Benevolent dictator |
| Meritocracy | Apache Foundation | Merit-based voting |
| Corporate | React (Meta), Angular (Google) | Company-controlled |
| Foundation | CNCF, Linux Foundation | Neutral home, vendor-neutral |

## Sustainability

- **Funding**: Sponsors (GitHub Sponsors, Open Collective), grants, consulting
- **Burnout**: Maintainers face high pressure — set boundaries
- **Community**: Contributors come and go; documentation reduces bus factor
- **Automation**: CI/CD, dependabot, issue templates reduce maintainer load

## Resources

- Choose a License: choosealicense.com
- Open Source Guides: opensource.guide
- GitHub Open Source: github.com/open-source

**Links**: [[Git Pull Requests]] | [[Git Workflows]] | [[Code Review Best Practices]] | [[Developer Workflow Automation]] | [[Documentation]]
