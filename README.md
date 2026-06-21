# benmilne.com Standards

Reference copies of the open formats we use to make the value layer legible. One small grammar
each, so humans and machines do not have to decide how to show, or how to document, the way value
moves.

## Formats

### GSF - Graph Standard Format ([./gsf](gsf))

Rules for visualizing relationships as D3 force-directed graphs. Structural and non-temporal: what
entities exist and how they connect. Built around four primitives (relationship, type, variables,
weight) with an achromatic base (Light is black and white) and color as an optional overlay.
Current: `v0.5.1`. Maintained here.

### CSF - Commons Stablecoin Format ([./csf](csf))

Rules for documenting funds flows as Mermaid sequence diagrams. Temporal and step-ordered: how
value moves from A to B, in what order, through which intermediaries. Vendored from
`Brale-xyz/commons` (`v1.4.7`, MIT (c) 2025 Brale). Upstream remains canonical; this is our
pinned reference.

## Why both

GSF and CSF are siblings. Both implement the three value-layer primitives - **ValueType** (what
moves), **TransferType** (how it moves), **Exchange** (converting between) - in two modalities:
GSF draws the structure, CSF draws the sequence.

### The value-layer profile in GSF

How GSF expresses the three primitives (a profile, not core grammar):

- **ValueType** - what moves. `node.type: concept`, `sub_type: value_type` (USD, USDC, SBC). The Issuer is inherent in the unit.
- **TransferType** - how it moves. `node.type: system`, `sub_type: transfer_type` (ACH, Wire, Solana, Canton).
- **Exchange** - converting between. A `transfers_via` link whose source/destination endpoint objects differ in `value_type` or `transfer_type`, with `via` naming the exchanger.

Mapping: an Issuer `issues` a ValueType; a TransferType `supports` a ValueType; a stablecoin
`represents` its fiat; a ValueType is `part_of` the Value Layer. Connect clusters through real
rails or a genuine containing concept, never a synthetic anchor.

### GSF and CSF interop

The same payment documents as a CSF sequence and a GSF graph. The mapping is deterministic, lossy
in one direction only (sequence order, since GSF is non-temporal):

| CSF | GSF |
|---|---|
| participant | node (type from role) |
| value leg (`->>`) | `transaction` link, or `transfers_via` when the leg converts |
| data leg (`-->>`) | `instruction` link |
| `[EXCHANGE]` | `transfers_via` with differing endpoints + `via` (self-referencing `via` for a self-loop) |
| Light / Medium / Heavy | `view.level` (same meaning: amount of detail) |
| sequence order | not represented - keep the CSF document if order matters |

`CSF -> GSF -> CSF` loses step ordering; `GSF -> CSF -> GSF` is lossless for structure.

## The value layer

Both formats are tools for the value-layer model. The essays, in order:

1. [The Value Layer of the Internet](https://benmilne.com/the-value-layer-of-the-internet) (2021) - the original framing: value moves as a ValueType (what) over a TransferType (how), anchored on value rather than geography.
2. [The Value Layer - Expanded](https://benmilne.com/the-value-layer-expanded) (2022) - the model put to work: a catalog of issuers, currencies, and transfer mechanisms across the global stack. The Kumu map this catalog lived in is what GSF is built to replace.
3. [Value Derivations](https://benmilne.com/value-derivations) (2025) - how the stack expands: stablecoins as liquidity derivations, protocols as transfer derivations, legacy rails going onchain. "Everything is a transfer."
4. [Better Funds Flows](https://benmilne.com/better-funds-flows) (2025) - the post that introduced CSF.
5. [The Value Layer](https://benmilne.com/book/the-value-layer) (2026) - the field guide that consolidates the three primitives.

## Licensing

- **GSF and this repository**: MIT, (c) 2026 Ben Milne ([LICENSE](LICENSE)).
- **CSF**: MIT, (c) 2025 Brale ([csf/LICENSE.brale](csf/LICENSE.brale)), vendored from
  https://github.com/Brale-xyz/commons at commit `3e696a6ee4a5d59c8e3a48d4a593dbf5d3a3d8e2`.
