---
name: diagrams
description: Use when preparing diagrams for architecture, flows, states, conditional logic, interactions, data models or customer journeys that a human needs to review. Draws every diagram with D2 in the hand-drawn style, renders it to PNG and checks the picture before it is shared. Not for screenshots, UI prototypes, data charts, or simple facts clearer in prose.
metadata:
  version: "0.7.0"
---

# Diagrams

Each diagram answers one question at a readable size. Draw every diagram with [D2](https://d2lang.com). Keep the editable `.d2` source and the verified PNG with the task, not in the skill folder. [references/features.md](references/features.md) shows the other D2 shapes and features: tables, classes, icons, links, tooltips, Markdown and LaTeX labels, globs and variables.

## Plan the diagram

Decide these four things before you write the source:

1. **The message.** Write in one sentence what a reader must see in five seconds, such as "every order goes through the outbox". If you cannot write it, you are about to draw a set of labelled boxes.
2. **The view.** Find it in the table below.
3. **The size.** 5 to 15 boxes read well. 15 to 25 boxes need groups. For more than 25 boxes, make two diagrams or use `layers`.
4. **The names.** Use the real names of the services, tables and events. Read the code if you can. `Service A -> Service B` tells the reader nothing.

| Question | View | How |
| --- | --- | --- |
| What connects, and where are the boundaries? | Boxes in named groups | "Box diagrams" below |
| Which conditions lead to which outcomes? | Flow, or a decision table if clearer | "Box diagrams" below |
| What does the customer do, including interruptions? | Flow that starts at a `shape: person` | "Box diagrams" below |
| Which states and transitions are possible? | State diagram | "State diagrams" below |
| Who sends what, in what order? | Sequence diagram | "Sequence diagrams" below |
| Which tables hold the data, and how do they join? | `shape: sql_table` | [references/features.md](references/features.md) |
| How is the code structured? | `shape: class` | [references/features.md](references/features.md) |
| How do variants of one system differ? | `scenarios` | "Boards" below |
| What happens at each stage, in order? | `steps`, as an animated SVG | "Boards" below |
| What is inside one part of an overview? | Overview with drill-down `layers` | "Boards" below |
| How do similar items compare, such as KPI tiles or a matrix? | Tiles with `grid-rows` and `grid-columns` | "Boards" below |

Use a chart library for data charts. D2 has no data binding or chart interaction.

Label conditions and meaningful transitions. For alternative diagrams, reuse the shape keys for the same steps. Say briefly whether the diagram shows proposed or observed behaviour, and what is uncertain. Put the title inside the picture. A page that shows the picture then needs no caption of its own. Never invent journey scores, and never treat a timeout as proof that work stopped.

## Write the source

- Connect keys, not labels. After `api: Orders API`, write `api -> db`. `Orders API -> db` makes a second box with no error.
- Put a label in quotes if it contains `#` or `;`. An unquoted `#` starts a comment, and an unquoted `;` starts a new shape. Both compile with no error.
- Style through `classes`, not through `style` on each box.
- Do not name a shape after a D2 keyword, such as `icon`, `label`, `style`, `link`, `layers`, `scenarios` or `steps`. The error does not name the cause. Use another key, and keep the word in the label.
- Do not put `d2-config` into `vars`. It breaks the house style. Give the layout, the theme and the sketch style as flags of the render command.

**House style.** Give each kind of part one fill. Use the same colours in every diagram. Soft fills with dark text stay readable in the sketch style. Name each part in words too, because colour alone is not enough. Paste this block at the top of the `.d2` file, and replace the title:

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

The two stroke lines give one-way and two-way edges the same grey. Without them, D2 draws edges blue. The font-size lines make the text large enough to stay readable at 2000 px wide. Set `class` on each box, including the boxes inside a group. Draw records as `shape: cylinder` and people as `shape: person`.

Draw no legend unless the reader asks for one. Then add only the kinds that the diagram uses:

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

## Box diagrams

Most long or looping lines come from the shape of the diagram, not from the tool. [checks/architecture.d2](checks/architecture.d2) is a full example. The rules in this file are complete without it.

- Make one main flow from left to right with `direction: right`.
- If every box inside a group gets or sends the same message, draw one edge to or from the group. Draw one edge to each box only when the messages differ.
- A box that receives from the left and sends to the right is a normal step on the main flow. Do not split it. A gateway, a sender or an outbox is such a step.
- Split a box only when one of its edges must go back to the left, to a box earlier in the main flow. Make an "in" box for the edges that it receives and an "out" box for the edges that it sends. Name them "<name> in" and "<name> out".
- Draw no edge between the two halves, because the facts give none. Put both halves in a group named after the box, such as `transport: Transport {in: Transport in; out: Transport out}`. Do not set `grid-columns` on this group. The frame shows that the halves are one part.
- Do not send a return path around the page. Put its two ends next to each other, or write it as a short note on the box. If a split box closes a loop across the page, do not split it. Write the return as a note. If one box is at both ends of the flow, draw it once at the start. Write the return as a note on the last box.
- An edge must not run around a group. If it does, move the boxes or split the diagram.
- Keep groups compact. Do not draw a frame with large empty areas. Set `grid-columns` on a group whose edges go to the group itself. An edge to a box inside a grid group breaks the ELK layout. If ELK pushes one box far from the others, move that box out of the group.
- ELK puts the boxes of a group in columns from left to right. A box with an edge that leaves the group goes to the group's left or right side. So a group is as tall as its tallest column. A tall column next to single boxes leaves empty corners in the frame.
- Keep the columns of a group close in height. Give a grid group of up to 4 boxes one row: set `grid-columns` to the number of its boxes. Never use `grid-columns: 1` for more than 2 boxes. Use more rows only for more than 4 boxes, or when step 4 below applies. Do not stack more than 3 groups in one column.
- Draw no group around the whole system that the diagram shows. The title names the system. Such a group is almost a frame around the page, and ELK leaves empty corners in it.
- Keep edge labels to two or three words.
- The text must stay readable when the picture is 2000 px wide. At `--scale 2`, keep the PNG at most 8000 px wide.
- Keep every word of every label that the facts give. Never shorten a label to fit the width. If the PNG is too wide, do these steps in order, and render again after each step:
  1. Break each label of more than two words into lines of one to three words with `\n`.
  2. Shorten the title. The top-left title takes its own column as wide as the title.
  3. Put boxes that get the same edge into one group.
  4. Give a grid group of 4 boxes two rows. A group of 3 boxes stays in one row.
  5. Split the diagram into two diagrams.

## State diagrams

Draw each state as a box with the `core` colours, and each transition as an edge labelled with its trigger. Draw the start as a small dark circle with no label. Give each end state a double border. Keep the house style block and `direction: right`. Example: [checks/state.d2](checks/state.d2).

```d2
classes: {
  start: {label: ""; shape: circle; width: 28; style: {fill: "#1F2328"; stroke: "#1F2328"}}
  end: {style: {fill: "#ECEAE4"; stroke: "#6B675F"; font-color: "#1F2328"; double-border: true}}
}
start: {class: start}
placed: Placed {class: core}
cancelled: Cancelled {class: end}
start -> placed
placed -> cancelled: customer\ncancels
```

## Sequence diagrams

Put the actors and messages in a container with `shape: sequence_diagram`. Example: [checks/sequence.d2](checks/sequence.d2).

- D2 draws the actors from left to right in the order of their first mention. It draws the messages from top to bottom in the order of the source.
- A sequence diagram ignores `direction` and the ELK layout.
- A `near` title takes its own column on the left. Use the container label as the title: set `label.near: outside-top-left`, the title font, and a transparent fill and stroke on the container.
- Draw replies with `style.stroke-dash: 3`.
- Put alternative paths in named groups inside the container, such as `approved: Approved {class: group; ...}`.
- Write `shop.t -> provider: Charge card` to draw an activation span on the `shop` lifeline.
- Write `provider."Retries 3 times"` to add a note on the `provider` lifeline.
- Write `shop -> shop: Save order` for a message to itself.

## Boards

- `scenarios`: Each scenario inherits the base board. Set `title.label`. Example: [checks/scenarios.d2](checks/scenarios.d2).
- `steps`: Each step inherits the previous board. Change only what differs. Render an animated SVG with `--animate-interval 1500`. Example: [checks/steps.d2](checks/steps.d2).
- `layers`: a layer starts empty. Repeat the styles, title and `classes`. Link the overview box with `link: layers.<name>`. Example: [checks/layers.d2](checks/layers.d2).
- Grid: Set `grid-rows` and `grid-columns` on a tile group without edges. Name each tile's state. Example: [checks/grid.d2](checks/grid.d2).

## Render and check

Rendering needs D2 and the Playwright CLI with its Chromium headless shell. `d2` with a `.png` output asks to install its own Chromium, so render an SVG and take a screenshot of it. Run this in the folder of the `.d2` file:

```sh
d2 fmt diagram.d2
d2 validate diagram.d2
d2 --layout elk --sketch --theme 0 --scale 2 diagram.d2 diagram.svg
for svg in diagram.svg diagram/*.svg; do
  [ -f "$svg" ] || continue
  size=$(grep -o -E 'width="[0-9]+" height="[0-9]+"' "$svg" | head -1 | tr -cs '0-9' ' ' | xargs | tr ' ' ',')
  playwright screenshot --viewport-size "$size" "file://$PWD/$svg" "${svg%.svg}.png"
done
```

- `d2 fmt` sets a standard layout of the source. `d2 validate` finds syntax errors without a render. Some errors show only in the render.
- If the diagram has icons, add `--bundle=false` to the render.
- Without animation, D2 writes one SVG per board into a folder.
- `--scale 2` gives a sharp picture. The `size` line reads the picture size from the SVG, because `--full-page` fails on an SVG file.
- If Playwright asks for a newer headless shell, run `playwright install --only-shell chromium`.
- The pictures are tested with D2 0.8.1 and 0.8.2 and Playwright 1.63.0. Record the versions that you used.

Open each PNG and check it in two passes.

**Pass 1: does it show the message?** The eye enters where the story starts and leaves where it ends. The flow keeps one direction. The groups match the parts that you planned. The most important part is easy to see.

**Pass 2: layout defects.** Look for these:

- an edge label far from its edge, or a label on a line or a border;
- crossing lines, or a line that goes around the page or a group;
- cut or overlapping text;
- one crowded area next to an empty one, or a frame with large empty areas;
- a line or a frame around the page;
- a PNG wider than 8000 px.

Fix a defect through the source, not through positions. Change the `direction`, change the groups, shorten edge labels, join parallel edges into one labelled edge, or move a part out of the flow with `near`. Then render and check again. Plan for two to four cycles. The diagram is done when both passes find nothing that needs an explanation. A diagram that only compiles is not checked. Mark it as unverified.

## Share the picture

- Give the `.d2` path and the PNG path. The `.d2` file is the source that a person edits.
- Show the PNG on its own white background. Keep it at its native size. Scale it down only while the labels stay readable. Never stretch it to fill a page. Split a dense diagram, or let it scroll, instead of making its text smaller.
- Give the picture alt text that states the message.
- A host such as a GitHub issue, PR or comment does not render D2. Attach the PNG with the `github-attachments` skill, and do not commit it.
- Amberstone `d2` evidence rejects boards. Send one PNG per board as `image` evidence.
- When a larger view helps, make the picture open it by tap, click or keyboard. The viewer has a meaningful title and a small close button. Escape closes it and returns focus. Keep focus visible. Do not add Expand, View source or Download source buttons.
