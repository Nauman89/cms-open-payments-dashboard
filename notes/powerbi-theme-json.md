# Power BI theme JSON: structure and traps

Reference note, written 15 September 2026 after a theme import was rejected.

## The error message is generic

"The JSON syntax was invalid, missing required property name, used invalid colors, or provided invalid top-level properties" covers four unrelated failures and does not say which one occurred. Work through them in order:

1. Validate the JSON itself, for example `python -c "import json;json.load(open('theme.json'))"`. A file can be valid JSON and still be rejected, so a pass here only rules out one cause.
2. Check `name` exists at the top level. It is the only required property.
3. Check every colour is a full six digit hex with a leading hash. Three digit shorthand, named colours and `rgba()` are all rejected.
4. Check the top level property names against the list below. This is the usual culprit, because an unrecognised key fails the whole file rather than being ignored.

## Valid top level properties

- `name` (required)
- `dataColors` (array of hex, the categorical sequence)
- `background`, `secondaryBackground`
- `firstLevelElements`, `secondLevelElements`, `thirdLevelElements`, `fourthLevelElements`
- `tableAccent`
- `good`, `neutral`, `bad`
- `maximum`, `center`, `minimum`, `null`
- `textClasses`
- `visualStyles`

## The trap: two generations of property names

Older theme files use `foreground`, `foregroundNeutralSecondary`, `foregroundNeutralTertiary`, `backgroundLight`, `backgroundNeutral`. Newer ones use the `firstLevelElements` through `fourthLevelElements` set plus `secondaryBackground`.

Mixing the two generations in one file fails. So does inventing a plausible looking name: `secondaryForeground` does not exist, and one key like that rejects the entire theme.

The safe approach is to use the newer set exclusively and nothing outside the list above.

## What the level elements actually control

- `firstLevelElements`: primary text, titles, data labels.
- `secondLevelElements`: secondary text, axis labels, legend text.
- `thirdLevelElements`: gridlines, borders, separators.
- `fourthLevelElements`: the most recessive chrome.

Setting these four means most visual formatting derives automatically, so `visualStyles` only needs to carry the things that are genuinely choices, such as title alignment and whether gridlines show.

## Diverging versus sequential conditional formatting

`minimum` and `maximum` alone give a two colour sequential scale. Adding `center` turns it into a three colour diverging scale. Leave `center` out unless a diverging scale is actually wanted.

## visualStyles shape

Three levels: visual name, card name, then an array holding one object of properties.

```
"visualStyles": { "<visualName or *>": { "<cardName or *>": { "<property>": [ { ... } ] } } }
```

Colours inside `visualStyles` take the object form `{ "solid": { "color": "#RRGGBB" } }`, not a bare hex string. Bare hex is correct at the top level and wrong here, which is an easy thing to get backwards.
