# mermaid-diagrams-skill

Readable diagrams with a consistent visual style: D2 in a hand-drawn style for
architecture and flows, Mermaid for sequences and states.

This is an agent skill. When someone has to review architecture, states,
conditional logic, an exchange of messages or a customer journey, the agent
picks the diagram type that fits the question. It draws architecture and flow
diagrams with D2, and sequence and state diagrams with Mermaid in a light and a
dark theme. It checks the rendered images before showing them.

## Why it exists, and what it turned down

Left alone, an agent tends to draw a flowchart for everything, use Mermaid's
default styling and call a diagram done once it parses. This skill makes it
choose the view by the question, render with a shared theme and look at the
result.

What it turned down:

- **Default Mermaid styling.** Two theme files set the type, surfaces and two
  meaning-carrying colours, so diagrams look alike across tasks.
- **Colour defined inside each diagram.** Colours live in the theme files.
  Diagrams only name a class: `decision` or `result`.
- **Invented journey scores.** Mermaid's native journey chart needs a score
  for each step. The skill draws journeys as unscored flowcharts unless real
  scores are supplied.
- **Parsing as proof.** A diagram that parses can still clip text or hide a
  label under a connector. The skill counts only an inspected render as
  verified.
- **Mermaid flowcharts for architecture.** Their layouts often loop lines
  around the page. D2 with the ELK layout gave the cleanest layout of the free
  tools tested (D2, Graphviz, LikeC4, Mermaid, Excalidraw). Its sketch mode
  gives a hand-drawn look.
- **Review-page building.** The skill makes diagrams. It does not build the
  page they are reviewed on, and it does not depend on any other skill.

## The design

The skill picks the view by the question the diagram answers. The view table
and appearance rules are in [SKILL.md](SKILL.md).

**Colour carries meaning.** The themes give decisions and results distinct
colours. Outcome names also make the meaning clear without colour.

**Branch labels stay legible.** Their background keeps connectors from running
through the text.

**Themes need a real renderer.** Markdown hosts such as GitHub use their own
Mermaid theme and ignore these files. Where appearance matters, the skill
shares verified images.

## What's where

| Path | Owns |
| --- | --- |
| `SKILL.md` | The rules the agent follows: view choice, appearance, presentation, render and verify |
| `assets/light.json`, `assets/dark.json` | Mermaid CLI configuration for each theme |
| `checks/*.mmd` | Five example diagrams about a fictional online shop, one per view, used to check a theme or renderer change |
| `checks/architecture.d2` | The architecture example in D2, with the colour palette and legend |
| `CHANGELOG.md` | What changed in each release |

The skill version is recorded in `metadata.version` in [SKILL.md](SKILL.md).
[CHANGELOG.md](CHANGELOG.md) records the changes for each version.

## How to run and verify it

Install the latest release for Claude Code and Codex with the
[skills CLI](https://github.com/vercel-labs/skills):

```sh
DO_NOT_TRACK=1 npx skills add https://github.com/asabirov/mermaid-diagrams-skill/tree/v0.3.0 --skill mermaid-diagrams --agent claude-code codex --global
```

`DO_NOT_TRACK=1` turns off the CLI's telemetry. To update or roll back, run
the same command with the release tag you want.

Dependencies:

| Dependency | Needed for |
| --- | --- |
| Node.js, npm and Git | Running the install command, and running Mermaid CLI |
| Claude Code or Codex | Running the skill |
| [Mermaid CLI](https://github.com/mermaid-js/mermaid-cli) (`@mermaid-js/mermaid-cli`, tested with 11.16.0) | Rendering diagrams to SVG with the light and dark themes. Without it the agent can write diagrams but cannot render or check them |
| The headless Chrome that Mermaid CLI downloads on install | Mermaid CLI draws each diagram in it |
| [D2](https://d2lang.com) (tested with 0.8.1) | Drawing architecture and flow diagrams. Without it the agent draws Mermaid flowcharts |
| [Playwright CLI](https://playwright.dev) with its Chromium headless shell (tested with 1.63.0) | Turning a D2 SVG into a PNG, because D2's own PNG export can hang |
| Geist font (optional) | Diagram text. Arial is used when Geist is not installed |

Install Mermaid CLI with:

```sh
npm install -g @mermaid-js/mermaid-cli@11.16.0
```

Then start a new agent session and ask for a diagram, for example "Show the
states an order goes through, including refunds." The agent should choose a
state diagram and render both themes.

### Check a theme or renderer change

The checks run locally, not in CI. From a checkout of this repository, render
every example in both themes:

```sh
mermaid_output=$(mktemp -d)
mmdc --version
for diagram in state sequence architecture logic journey; do
  for mode in light dark; do
    mmdc -q -i "checks/$diagram.mmd" -o "$mermaid_output/$diagram-$mode.svg" \
      -c "assets/$mode.json" -b transparent
  done
done
ls "$mermaid_output"
```

Output on 2026-10-03:

```text
11.16.0
architecture-dark.svg
architecture-light.svg
journey-dark.svg
journey-light.svg
logic-dark.svg
logic-light.svg
sequence-dark.svg
sequence-light.svg
state-dark.svg
state-light.svg
```

Render the D2 example with the commands in the "Render and verify" section of
[SKILL.md](SKILL.md), from a copy of `checks/architecture.d2` named
`diagram.d2`.

Ten files mean every Mermaid example parsed and rendered. They do not prove the
diagrams look right. Open each one on a page of the matching background
colour. Check the branch labels, the blue and teal fills, the subgraph borders
and the text at desktop and tablet widths.

To check a change to `SKILL.md`, give the same review request to the old and
the new version. Compare the view chosen, the source files kept, the
appearance and how honestly each states what it did not verify. Also send a
nearby request that should not produce a diagram, such as a punctuation fix.

## Licence

[MIT](LICENSE), © 2026 Artur Sabirov.
