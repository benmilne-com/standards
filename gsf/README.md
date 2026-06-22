# Graph Standard Format (GSF)

**Status:** Draft `v0.5.2`

GSF is a small set of rules for visualizing relationships. It is the grammar a human or a
machine follows so they do not have to decide how to show a relationship graph.

GSF is the rules, not the data:

- **GSF** is the ruleset (this repo). It is never visualized.
- **A dataset** is a `{view, nodes, links}` instance that conforms to GSF. This is what gets visualized.
- **A renderer** takes the rules plus a dataset and draws the picture.

Like [CSF](https://github.com/Brale-xyz/commons) (its sibling for funds-flow sequence
diagrams), GSF keeps the grammar small so the catalog can stay large.

## The primitives

Four primitives. Everything a renderer needs is one of these, so nobody has to decide what a
color or a size means.

- **Relationship** - a link between exactly two entities. Links create the graph; nodes only annotate.
- **Type** - the qualitative classifier. `link.type` is what the relationship means; `node.type` is what the entity is. Drives **shape** (always) and **color** (from Medium).
- **Variables** - the detail bag. Holds everything `type` does not.
- **Weight** - the quantitative magnitude. Drives **size and width**.

The core encoding rule is **value vs values**: the one quantitative *value* (a magnitude, carried
in `weight`) drives size and width; the qualitative *values* (status, category, label) drive
color, grouping, and what shows on hover.

## Density: Light / Medium / Heavy

The level is a disclosure and chroma dial - how much detail *and color* is revealed, not how much
graph. It is a rule, so the same level renders the same way everywhere (and matches CSF's
Light/Medium/Heavy). **Light is black and white**, like a CSF sequence diagram: color is a later,
optional layer.

- **Light** - the story in black and white. Type by **shape**, relationship class by **line style**
  (solid = value, dashed = data, dotted = derived), magnitude by **thickness**, direction by
  **arrowhead**. No color. Fast and print-ready.
- **Medium** - adds **color** as an overlay (node and edge color by type), a legend, and variables on hover.
- **Heavy** - the full record. All variables, transfer endpoint detail, expansion affordances.

Color is never the only signal: remove it and shape, line style, thickness, and arrowhead still
tell the story. Breadth (how many hops from focus) is a separate knob: `view.hops`.

## The value layer

GSF grounds out in the [value layer](https://benmilne.com/book/the-value-layer) model as a
profile: **ValueType** (what moves) is a `concept` node, **TransferType** (how it moves) is a
`system` node, and an **Exchange** (converting between) is a `transfers_via` link whose endpoint
objects differ and whose `via` names the exchanger. The Issuer is inherent in the ValueType.

## Minimal dataset

```json
{
  "format": "gsf",
  "version": "0.5.2",
  "renderer": "d3-force",
  "view": { "level": "light", "hops": 1, "focus": ["Person A"] },
  "links": [
    { "source": "Person A", "destination": "Person B", "type": "message" }
  ]
}
```

A link needs `source`, `destination`, and `type`. Everything else is optional. Force-directed
layout is the canonical default: topology is the signal, so structure emerges from the graph.

## Relationship to CSF

| | CSF | GSF |
|---|---|---|
| Output | Mermaid sequence diagrams | D3 relationship graphs |
| Answers | *How* value steps from A to B, in order | *What* exists and how it is connected |
| Nature | Temporal, step-ordered | Structural, non-temporal |
| Levels | Light / Medium / Heavy (detail) | light / medium / heavy (detail) |

Both implement the same three value-layer primitives. The deterministic GSF <-> CSF mapping (and
the value-layer profile) lives in the [standards README](../README.md); the one known loss is
sequence order, since GSF is non-temporal.

## What's here

- **[`gsf-0.5.2.json`](gsf-0.5.2.json)** - the rules: primitives, encoding, density, grammar
  (link/node/variables/weight), the seven entity types, relationship-type conventions,
  anti-patterns, validation, `llm_instructions` (a drop-in prompt block), and 3 worked example
  datasets.
- **[`demo/gsf-embed.html`](demo/gsf-embed.html)** - a self-contained D3 reference renderer
  (only dependency is the D3 v7 CDN). A Light/Medium toggle shows the achromatic base (shape +
  line style, fully B&W) versus the color overlay. Hover any node or edge for its type and
  metadata, including a transfer's value/transfer types and `via` exchange.

## Using it

Paste `gsf-0.5.2.json` into an LLM and ask it to convert source material into a GSF dataset, or
hand it to a renderer as the format contract. The `llm_instructions` and `validation` sections
are written to be machine-consumed.

## License

MIT - see [`LICENSE`](LICENSE). Attribution appreciated.
