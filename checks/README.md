# Check a renderer or style change

The checks run locally, not in CI. From the root of a checkout of this
repository, render every example to PNG:

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

Output on 2026-11-18:

```text
v0.9.0
Version 1.65.0
./architecture.png
./classes.png
./flow.png
./grid.png
./journey.png
./layers/index.png
./layers/shop.png
./scenarios/index.png
./scenarios/payment-down.png
./sequence.png
./state.png
./steps/charged.png
./steps/index.png
./steps/shipped.png
./tables.png
```

The scenarios, steps and layers examples give one PNG per board. Open each PNG
and check it with the two passes in SKILL.md.

To check a change to `SKILL.md`, give the same review request to the old and
the new version. Compare the view chosen, the source files kept, the
appearance and how honestly each states what it did not verify. Also send a
nearby request that should not produce a diagram, such as a punctuation fix.
