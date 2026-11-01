# Product Brief and Glossary

Structure of `product-brief.md` and `glossary.md`. Keep every section. Write `none` if empty. Short bullets. Every fact cites `[[n]](#references)`. `(inferred)` on every claim whose evidence is names or structure. Caps: 120 lines for the brief, 80 for the glossary.

````md
# Product Brief

Generated from commit <sha> on <date>.

## What it is
- <1-3 bullets: purpose, users, value> [[n]](#references)

## Actors
- <actor> - <what they do> [[n]](#references)

## Core features
- <feature> - <one line> [[n]](#references) (inferred)

## Supportive areas
- <area> - <one line> [[n]](#references)

## Core operations
- <verb noun> - <trigger -> outcome> [[n]](#references)

## External systems
- <system> - <role> [[n]](#references)

## Scope
- In: <bullet per item> [[n]](#references)
- Out: <bullet per item, only when documented or stated by the user> [[n]](#references)

## Diagrams

```mermaid
flowchart LR
  %% Context: actors, the product, external systems. At most 12 nodes. Labels are names only.
```

```mermaid
flowchart TD
  %% One core operation: trigger to outcome. At most 12 nodes.
```

## References
- [1] <type: doc | link | file | user answer> - <`path`, URL or "User, chat"> (mark unreadable ones)
````

````md
# Glossary

Generated from commit <sha> on <date>.

Terms, abbreviations and internal names specific to this product. Alphabetical.

| Term | Meaning | Source |
|---|---|---|
| <term or abbreviation> | <meaning, or `meaning unknown`> | [[n]](#references) (inferred) |

## References
- [1] <type: doc | link | file | user answer> - <`path`, URL or "User, chat">
````
