---
name: diagrams
description: Use when preparing diagrams for architecture, flows, states, conditional logic, interactions, or customer journeys that a human needs to review. Draws architecture and flow diagrams with D2 in the hand-drawn style, and sequence and state diagrams with Mermaid. Not for screenshots, UI prototypes, or simple facts clearer in prose.
metadata:
  version: "0.4.0"
---

# Diagrams

Each diagram answers one question at a readable size. Keep the editable `.d2` or `.mmd` source and the verified renders with the task, not in the skill folder.

## Choose the view

| Question | View | Tool |
| --- | --- | --- |
| What connects, and where are the boundaries? | Boxes in named groups | D2 |
| Which states and transitions are possible? | State diagram | Mermaid |
| Who sends what, in what order? | Sequence diagram | Mermaid |
| Which conditions lead to which outcomes? | Flow, or a decision table if clearer | D2 |
| What does the customer do, including interruptions? | Unscored flow; Mermaid's native journey only with supplied scores | D2 |

Use D2 for architecture and flow diagrams, because its ELK layout and sketch style read better than Mermaid flowcharts. Use Mermaid for sequence and state diagrams, and when a host must render the source itself, such as a GitHub comment. If D2 is not installed, draw a Mermaid flowchart and say so.

Label conditions and meaningful transitions. For alternative diagrams, reuse node IDs for the same steps. Say briefly whether the diagram shows proposed or observed behavior, and what is uncertain. Put the title inside the picture. In D2, put it at the top left, as the block below shows. In Mermaid, set `title` in the front matter. Mermaid draws it at the top centre. A page that shows the picture then needs no caption of its own. Never invent journey scores, and never treat a timeout as proof that work stopped.

## D2 diagrams

**Shape the diagram so the layout stays clean.** Most long or looping lines come from the shape of the diagram, not from the tool.

- Make one main flow from left to right with `direction: right`.
- If every box inside a group gets or sends the same message, draw one edge to or from the group. Draw one edge to each box only when the messages differ.
- Split a box that both sends and receives on the main flow into an "in" box and an "out" box. The "in" box receives and the "out" box sends. Name them "<name> in" and "<name> out", and put both in the same group.
- Do not send a return path around the page. Put its two ends next to each other, or write it as a short note on the box. If a split box closes a loop across the page, do not split it. Write the return as a note. If one box is at both ends of the flow, draw it once at the start. Write the return as a note on the last box.
- An edge must not run around a group. If it does, move the boxes or split the diagram.
- Keep groups compact. Do not draw a frame with large empty areas. Set `grid-columns` on a group whose edges go to the group itself. An edge to a box inside a grid group breaks the ELK layout. If ELK pushes one box far from the others, move that box out of the group.
- Keep edge labels to two or three words.
- The text must stay readable when the picture is 2000 px wide. At `--scale 2`, keep the PNG at most 8000 px wide. The top-left title takes its own column as wide as the title, so keep it short. If the PNG is still too wide, put boxes that get the same edge into one group. Then remove boxes or split the diagram.

**Colour carries meaning.** Give each kind of part one fill. Use the same colours in every diagram. Soft fills with dark text stay readable in the sketch style. Name each part in words too, because colour alone is not enough. Paste this block at the top of the `.d2` file, and replace the title:

```d2
direction: right
(** -> **)[*].style.stroke: "#57606A"
(** <-> **)[*].style.stroke: "#57606A"
(** -> **)[*].style.font-size: 24
(** <-> **)[*].style.font-size: 24
**.style.font-size: 28

title: "Orders: how a request is stored" {
  near: top-left
  shape: text
  style: {font-size: 40; bold: true; font-color: "#1F2328"}
}

classes: {
  # outside: people, browsers and other companies' systems
  outside: {style: {fill: "#ECEAE4"; stroke: "#6B675F"; font-color: "#1F2328"}}
  # way in: what first receives a request, such as a web app, API or queue
  way-in: {style: {fill: "#DCEAF7"; stroke: "#2F6596"; font-color: "#1F2328"}}
  # core: the services and jobs that do the work
  core: {style: {fill: "#DDF0E4"; stroke: "#2E7350"; font-color: "#1F2328"}}
  # records: databases, files and other stored data
  records: {style: {fill: "#F8EBCB"; stroke: "#8F6A12"; font-color: "#1F2328"}}
  # group: a boundary around boxes, such as a system or a team
  group: {style: {fill: "#FAFAF8"; stroke: "#8C959F"; font-color: "#1F2328"}}
}
```

The second stroke line gives two-way edges the same grey as one-way edges. Without it, D2 draws them blue. The font-size lines make the text large enough to stay readable at 2000 px wide. Set `class` on each box, including the boxes inside a group. Draw records as `shape: cylinder`. [checks/architecture.d2](checks/architecture.d2) is a full example. The rules in this file are complete without it.

Draw no legend in a D2 or Mermaid diagram unless the reader asks for one. In D2, add only the kinds that the diagram uses:

```d2
vars: {
  d2-legend: {
    a: Outside {class: outside}
    b: Way in {class: way-in}
    c: Core {class: core}
    d: Records {shape: cylinder; class: records}
  }
}
```

## Mermaid appearance

Use [assets/light.json](assets/light.json) and [assets/dark.json](assets/dark.json). Both use 20px Geist or Arial text, quiet surfaces and neutral connectors. Subgraphs sit on the page background inside a thin border. Colour carries meaning: blue for `decision`, teal for `result`; other nodes stay neutral. Assign classes with `class NODE_ID decision` or `class NODE_ID result`. Sequence participants use blue surfaces. Keep colours in the theme files, not in `classDef` lines. Name each outcome in words; colour alone is not enough.

Use soft fills without decorative node outlines. Branch labels need an opaque background and space around them so connectors do not cross the text; check both themes. Around a diagram, avoid numbered sections, repeated metadata, divider lines, nested borders and collapsible blocks.

Default to solid links. Non-solid links must mark needed distinctions, including optional steps. Explain each meaning in edge labels or in a caption. Flowcharts use solid `-->` and dotted `-.->`. Sequence `->>` messages are solid. Keep dotted `-->>` replies, solid async `-)` arrows, and dotted async `--)` arrows. When `->>` or `-->>` messages mix with `-)` or `--)` messages, label sync, reply, and async messages in their text. Never add dotted or dashed decoration.

## Review presentation

Show a D2 picture on its own white background. Render a Mermaid diagram with a transparent background. Follow the page’s light or dark theme and its theme switch; do not add a separate theme menu. Keep each SVG at its native size: scale down only while labels stay readable, and never stretch it to fill a page or viewer. Split a dense diagram or let it scroll instead of shrinking its text.

When a larger view helps, make the diagram itself open it by tap, click or keyboard. The viewer has a meaningful title and a small close button; Escape closes it and returns focus. Keep focus visible. Do not add Expand, View source or Download source buttons; keep source files with the task, out of the reviewer’s way. Give the page a short title a person would use, set apart by clear typographic hierarchy, not an internal key or a generic report heading.

## Render and verify

D2 needs [D2](https://d2lang.com) and the Playwright CLI with its Chromium headless shell. D2's own PNG export can hang, so render an SVG and take a screenshot of it. Run this in the folder of the `.d2` file:

```sh
d2 --layout elk --sketch --theme 0 --scale 2 diagram.d2 diagram.svg
size=$(grep -o -E 'width="[0-9]+" height="[0-9]+"' diagram.svg | head -1 | tr -cs '0-9' ' ' | xargs | tr ' ' ',')
playwright screenshot --viewport-size "$size" "file://$PWD/diagram.svg" diagram.png
```

`--scale 2` gives a sharp picture. The second line reads the picture size from the SVG, because `--full-page` fails on an SVG file. If Playwright asks for a newer headless shell, run `playwright install --only-shell chromium`. Look at the PNG before you send it. Check that the PNG is at most 8000 px wide, so that the text is readable at 2000 px wide. Fix crossing lines, lines that go around the page or a group, frames with large empty areas, labels on lines or borders, and a line or frame around the page. Attach the PNG where the diagram is shown. The pictures are tested with D2 0.8.1 and 0.8.2 and Playwright 1.63.0. Record the versions you used.

Mermaid requires Mermaid CLI (`mmdc`) with Node.js and the browser it drives, or another renderer. Use what is already installed. The themes are tested with Mermaid CLI 11.16.0; record the version you used.

```sh
mmdc -i /path/to/diagram.mmd -o /path/to/diagram-light.svg -c /path/to/diagrams/assets/light.json -b transparent
```

Render the dark version with `dark.json`. Include `accTitle` and `accDescr`, and give the image useful alt text where it is shown. Look at both themes at the intended desktop and tablet sizes for clipped text, overlaps, labels crossing connectors and weak contrast. Test any viewer by touch, click and keyboard. Keep the renderer version and the evidence with the task. A diagram that parses has not been checked visually; mark unrendered work as unverified. Markdown hosts such as GitHub ignore these themes, so use verified images when appearance matters.

`dark.json` defaults to page colour `#0a0a0a`, and you keep other dark copies with the task, outside the skill folder. If the page differs, set `themeVariables.background` and `themeVariables.clusterBkg` to its colour. Match the `.edgeLabel rect` fill and stroke and the `.messageText`, `.loopText` and `.sectionTitle` strokes in `themeCSS` to the page colour.
