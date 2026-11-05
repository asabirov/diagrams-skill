# diagrams-skill

Readable diagrams with a consistent visual style, all drawn in D2 in a
hand-drawn style.

This is an agent skill. When someone has to review architecture, states,
conditional logic, an exchange of messages, a data model or a customer journey,
the agent picks the diagram type that fits the question. It draws every
diagram with D2, renders it to PNG and checks the picture before it shows it.

## Why it exists, and what it turned down

Left alone, an agent tends to draw a flowchart for everything, use the default
styling and call a diagram done once it parses. This skill makes it choose the
view by the question, use one shared palette and look at the result.

What it turned down:

- **Two diagram languages.** Earlier versions drew sequence and state diagrams
  with Mermaid. D2 draws both, so one language, one palette and one render
  loop cover every view.
- **Colour defined inside each box.** One `classes` block names each kind of
  part. Each box only names its class.
- **Invented journey scores.** The skill draws journeys as flows that start at
  a person, with no scores unless real scores are supplied.
- **Parsing as proof.** A diagram that compiles can still clip text or hide a
  label under a line. The skill counts only a checked render as verified.
- **Other layout tools.** D2 with the ELK layout gave the cleanest layout of
  the free tools tested (D2, Graphviz, LikeC4, Mermaid, Excalidraw). Its sketch
  mode gives a hand-drawn look.
- **Review-page building.** The skill makes diagrams. It does not build the
  page they are reviewed on.

## The design

The skill picks the view by the question the diagram answers. The view table,
the house style and the render loop are in [SKILL.md](SKILL.md). Rarer shapes
and features are in [references/features.md](references/features.md), which
the agent reads only when a diagram needs them.

**Colour carries meaning.** Each kind of part, such as a way in, a core
service or stored records, has one fill. Labels name each part, so the meaning
is clear without colour.

**The picture is the output.** Hosts such as GitHub do not render D2. The
agent shares the checked PNG, and attaches it to an issue or a PR without a
commit.

## What's where

| Path | Owns |
| --- | --- |
| `SKILL.md` | The rules the agent follows: plan, view choice, house style, layout, render and check |
| `references/features.md` | Tables, classes, icons, links, tooltips, Markdown and LaTeX labels, globs, variables and reserved names |
| `checks/architecture.d2` | A box diagram with groups, the palette and the title inside the picture |
| `checks/sequence.d2`, `checks/state.d2` | A sequence diagram with alternative paths, and a state diagram with start and end states |
| `checks/scenarios.d2`, `checks/steps.d2`, `checks/layers.d2`, `checks/grid.d2` | One example for each kind of board: variants, stages in order, a drill-down and a tiled board |
| `CHANGELOG.md` | What changed in each release |
| `.github/workflows/notify-claude-2.yml` | Tells agents-home about each merge to `main`, so that it can move its pin of this skill |

The examples are about a fictional online shop. The skill version is recorded
in `metadata.version` in [SKILL.md](SKILL.md). [CHANGELOG.md](CHANGELOG.md)
records the changes for each version.

## How to run and verify it

Install the latest release for Claude Code and Codex with the
[skills CLI](https://github.com/vercel-labs/skills):

```sh
DO_NOT_TRACK=1 npx skills add https://github.com/asabirov/diagrams-skill/tree/v0.6.0 --skill diagrams --agent claude-code codex --global
```

`DO_NOT_TRACK=1` turns off the CLI's telemetry. To update or roll back, run
the same command with the release tag you want.

Dependencies:

| Dependency | Needed for |
| --- | --- |
| Node.js, npm and Git | Running the install command |
| Claude Code or Codex | Running the skill |
| [D2](https://d2lang.com) (tested with 0.8.1) | Drawing every diagram. Without it the agent can write diagrams but cannot render or check them |
| [Playwright CLI](https://playwright.dev) with its Chromium headless shell (tested with 1.63.0) | Turning a D2 SVG into a PNG, because D2's own PNG export installs its own Chromium |
| Geist font (optional) | Diagram text. Arial is used when Geist is not installed |

Then start a new agent session and ask for a diagram, for example "Show the
states an order goes through, including refunds." The agent should choose a
state diagram, render it and check the PNG.

### Check a renderer or style change

The checks run locally, not in CI. From a checkout of this repository, render
every example to PNG:

```sh
out=$(mktemp -d)
d2 --version
playwright --version
for src in checks/*.d2; do
  name=$(basename "$src" .d2)
  d2 validate "$src" >/dev/null || echo "invalid: $src"
  d2 --layout elk --sketch --theme 0 --scale 2 "$src" "$out/$name.svg" 2>/dev/null
  for svg in "$out/$name.svg" "$out/$name"/*.svg; do
    [ -f "$svg" ] || continue
    size=$(grep -o -E 'width="[0-9]+" height="[0-9]+"' "$svg" | head -1 | tr -cs '0-9' ' ' | xargs | tr ' ' ',')
    playwright screenshot --viewport-size "$size" "file://$svg" "${svg%.svg}.png" >/dev/null
  done
done
(cd "$out" && find . -name '*.png' | sort)
```

Output on 2026-11-05:

```text
v0.8.1-HEAD
Version 1.63.0
./architecture.png
./grid.png
./layers/index.png
./layers/shop.png
./scenarios/index.png
./scenarios/payment-down.png
./sequence.png
./state.png
./steps/charged.png
./steps/index.png
./steps/shipped.png
```

The scenarios, steps and layers examples give one PNG per board. Open each PNG
and check it with the two passes in SKILL.md.

To check a change to `SKILL.md`, give the same review request to the old and
the new version. Compare the view chosen, the source files kept, the
appearance and how honestly each states what it did not verify. Also send a
nearby request that should not produce a diagram, such as a punctuation fix.

## Licence

[MIT](LICENSE), © 2026 Artur Sabirov.
