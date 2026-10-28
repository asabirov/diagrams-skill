# Changelog

## Unreleased

### Changed

- **Compact group frames.** The skill says why ELK leaves empty corners in a group frame. A grid group of up to 4 boxes gets one row, and no group stacks more than 3 groups in one column. The agent draws no group around the whole system, because the title names it.

- **Full labels.** The agent keeps every word of every label. If the PNG is wider than 8000 px, it breaks labels into lines, shortens the title, groups boxes, uses two grid rows, and only then splits the diagram. It no longer removes boxes to fit the width.

- **Split rule.** A box that receives from the left and sends to the right is not split. The agent splits a box only when one of its edges goes back to an earlier box. It draws no edge between the halves, and puts them in a group named after the box.

## 0.4.0 — 2026-10-28

### Changed

- **New name.** The skill is now `diagrams`, and the repository is `asabirov/diagrams-skill`. The skill draws D2 and Mermaid diagrams, not only Mermaid.

- **Title inside the picture.** The agent puts the title at the top left of a D2 picture. A page that shows the picture needs no caption of its own.

- **Legend only on request.** The agent draws no legend in a D2 or Mermaid diagram unless the reader asks for one. Mermaid link and message kinds are named in their labels.

- **Two-way edges.** Edges with arrows at both ends get the same grey stroke as one-way edges. Before, D2 drew them blue.

- **Layout rules.** The agent names split boxes "<name> in" and "<name> out" in the same group, keeps groups compact, and does not run an edge around a group. A split box that closes a loop across the page stays one box with a note. The base block sets larger text, and a PNG at `--scale 2` stays at most 8000 px wide, so that the text is readable at 2000 px wide. If it is wider, the agent removes boxes or splits the diagram.

### Added

- **Release notice.** Each merge to `main` tells agents-home, so that its pin of this skill moves without the daily sync.

## 0.3.0 — 2026-10-28

### Added

- **D2 diagrams.** The agent draws architecture and flow diagrams with D2, with the ELK layout and the hand-drawn sketch style. Mermaid stays for sequence and state diagrams, and for hosts that render the source themselves. If D2 is missing, the agent draws a Mermaid flowchart and says so.

- **Colour palette.** Each kind of part has one soft fill: grey for the outside, blue for the way in, green for the core and sand for stored records. A small legend names each colour.

- **Clean layout rules.** The skill tells the agent how to shape a diagram so that lines stay short and labels stay off borders, and how to render a PNG through Playwright because D2's own PNG export can hang.

## 0.2.1 — 2026-10-06

### Changed

- **Link styles.** The agent uses solid links by default, explains needed non-solid links, and identifies solid and dotted async arrows.

- **Sequence arrows.** The agent labels mixed sync, reply, and async messages in text or a legend so you can distinguish dotted replies from dotted async messages.

### Fixed

- **Edge label spacing.** You see even space around flowchart and state labels in both themes.

- **Sequence labels.** You can read message and condition labels where they cross lifelines.

- **Dark edge labels.** You see full letters and hidden connectors on #0a0a0a, and matching theme copies blend labels into other dark pages.

## 0.2.0 — 2026-10-04

### Changed

- **Subgraphs are readable.** Subgraphs sit on the page background inside a thin border in both themes, so the nodes inside them stay visible.

- **Examples about a fictional online shop.** The five check diagrams cover an order lifecycle, card payment, shop architecture, a refund decision and an item return.

- **Tested version stated in the skill.** The skill names Mermaid CLI 11.16.0 as the tested renderer instead of pointing to the README.

## 0.1.0 — 2026-09-29

Mermaid diagrams helps you get a readable diagram for architecture, states, conditional logic, interactions or a customer journey that a person needs to review.

### Added

- **View selection by question.** The skill picks a flowchart, state diagram, sequence diagram, decision table or unscored journey based on the question being answered, rather than defaulting to one diagram type. This keeps the diagram matched to what actually needs explaining.

- **Light and dark theme assets.** You get diagrams rendered with consistent typography, quiet surfaces and semantic colour roles for decisions and results, in both a light and a dark theme. This keeps diagrams readable and consistent instead of relying on default Mermaid styling.

- **Reviewable presentation rules.** The skill renders diagrams at their native size, matches the host page's light/dark theme, and, when a larger view helps, makes the diagram tappable and keyboard operable without exposing source or download controls. This keeps the reviewer's focus on the diagram itself.

- **Render-and-verify workflow.** The skill renders both themes with Mermaid's command-line renderer and inspects them, with five example diagrams to test against, before a diagram is shared. This catches clipping, collisions and contrast problems before a reviewer sees them.
