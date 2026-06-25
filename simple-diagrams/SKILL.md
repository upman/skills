---
name: simple-diagrams
description: Create simple, legible block diagrams and sequence diagrams in HTML, SVG, Mermaid, or similar lightweight formats. Use when an agent needs to explain system architecture, request flows, fanout paths, deployment topology, data movement, trace/media upload paths, or any workflow where boxes, arrows, labels, and ordering must be visually clear.
---

# Simple Diagrams

## Core Workflow

1. Identify the diagram type before drawing:
   - Use a block diagram for static architecture, ownership boundaries, pods/services, storage, external systems, and deployment shape.
   - Use a sequence diagram for request order, retries, fanout, async handoffs, state creation/consumption, or "who calls whom".
   - Use both when the static topology and the runtime flow answer different questions.

2. List the participants first. Keep names stable across all diagrams. If two diagrams appear on the same page, use the same left-to-right order where possible.

3. Draw the critical happy path first. Add optional paths, failure paths, toggles, and caveats only after the main flow is readable.

4. Validate the diagram against the implementation or source notes. Diagram labels should not invent behavior; if the SDK sets an attribute and a gateway only routes on it, say that.

5. Confirm facts before committing them to the diagram. Read the relevant code, docs, configs, traces, logs, or user-provided source material; if a fact cannot be verified, mark it as an assumption or leave it out.

## Bundled Examples

- For a concrete fanout/demultiplexing pattern with central and tenant export paths, read [examples/shareable-demultiplexing.md](examples/shareable-demultiplexing.md).

## Layout Rules

- Reserve real gaps between boxes for arrow labels. Do not place labels in gaps narrower than the label text.
- Prefer left-to-right flow for block diagrams and sequence diagrams unless the domain has a stronger convention.
- Align related boxes by row or column. If the block diagram and sequence diagram appear together, align their participant order.
- Use wider diagrams before shrinking text. Increase the SVG `viewBox` or canvas width when labels are crowded.
- Show scalable replicas with two or three offset backing rectangles behind the main box. Keep the front box readable.
- Use separate arrows for separate fanout destinations. If central and tenant exports both happen, show two arrows and label them separately.
- Keep storage/state boxes near the request step that creates or consumes the state. Explain why state exists when requests can land on different pods.
- When an arrow label sits on or near an arrow, add a light background rectangle behind the text.
- Avoid long labels on arrows. Use short labels on the diagram and move nuance into nearby notes.
- Do not let text overflow its box. Wrap into multiple lines, widen the box, or shorten the wording.

## Visual Style

- Default to black text and lines on white, with one light grey fill for secondary/internal sections.
- Use square or nearly square corners for technical diagrams.
- Keep stroke widths consistent: boxes and primary arrows should visually match.
- Use dashed arrows for secondary/metadata/control paths; use solid arrows for primary data/request paths.
- Use muted grey text for notes and implementation details.
- Avoid decorative colors unless color carries meaning. If color carries meaning, add labels so the diagram still works in grayscale.
- Use monospace only for paths, env vars, identifiers, keys, and short protocol labels.

## Label Hygiene

- Prefer actor labels that state responsibility:
  - Good: `Routes tenant-marked traces`
  - Risky: `Adds tenant route attributes` unless the component truly creates them.
- Make destination labels precise:
  - `tenant destination` or `region key` if the value is `eu`, `us`, or `jp`.
  - Avoid calling a region key a tenant id.
- Name state concretely:
  - Prefer `upload URLs` over vague terms like `plan` when the stored payload is a URL bundle.
  - If state includes extra fields, mention them in prose: destination name, expiry, content length, etc.
- For request paths, include the important endpoints in the responsible box, not only on arrows.
- For repeated operations, label each arrow with the destination or role: `central trace`, `tenant trace`, `central bytes`, `tenant bytes`.

## Sequence Diagram Tips

- Use participants as column headers and keep lifelines aligned.
- Group related phases with subtle background bands, not heavy nested boxes.
- Number steps when the order matters or when prose references the diagram.
- Use `(2a)` / `(2b)` style labels for parallel fanout branches.
- Put shared-state operations visibly between the create step and the consume step.
- For asynchronous or cross-pod behavior, call out why shared state is needed.
- If a trace later contains a marker created earlier, show the marker near the trace export step.

## Block Diagram Tips

- Separate the main system path from existing or unrelated infrastructure with a small note or compressed lane.
- Avoid making secondary lanes as visually heavy as the main architecture.
- Show internal collectors/gateways as separate boxes when they have different responsibilities.
- Keep external systems on the right when requests leave the application boundary.
- Use dashed separators sparingly to divide conceptual sections.

## Review Checklist

Before finishing:

- Check every arrow has a clear source, target, and meaning.
- Check no label overlaps an arrow or box edge without a background.
- Check text fits inside boxes at the expected browser width.
- Check repeated diagrams use consistent participant names and order.
- Check central/tenant or primary/secondary fanout paths are visually distinct.
- Check the diagram does not claim a component creates, stores, or forwards data it does not.
- Check factual claims against source material, especially who sets data, who forwards data, what is stored, and which paths/endpoints are used.
- If editing HTML/SVG, run `git diff --check`.
