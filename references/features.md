# D2 features

Read this file when a diagram needs a shape or a feature that SKILL.md does not show. Each snippet renders with D2 0.8.1 and the render command in SKILL.md. Keep the house style block of SKILL.md at the top of the file.

## Special shapes

**Person.** Use `shape: person` for a human actor, in a box diagram or a sequence diagram.

```d2
customer: Customer {shape: person; class: outside}
```

**Database table.** Use `shape: sql_table` for a data model. Each child is a column. Its value is the type. Connect columns, not tables, so that the edge shows the key.

```d2
users: {
  shape: sql_table
  id: int {constraint: primary_key}
  email: varchar {constraint: unique}
}
orders: {
  shape: sql_table
  id: int {constraint: primary_key}
  user_id: int {constraint: foreign_key}
}
users.id <-> orders.user_id
```

The constraints show as `PK`, `FK` and `UNQ`.

**Class.** Use `shape: class` for a code structure. A `+` member is public, `-` is private and `#` is protected. Put `#` members in quotes, because an unquoted `#` starts a comment. A member with `()` is a method, and its value is the return type.

```d2
Order: {
  shape: class
  +id: int
  -total: decimal
  "#items": list
  +pay(): bool
}
```

A `sql_table` and a `class` have a dark header by default. Set `style.fill` on the shape to change the header colour.

**Code.** Put a short code sample in a block with its language name.

```d2
handler: |go
  func Pay(o Order) error {
    return charge(o.Total)
  }
|
```

## Text

**Markdown label.** Use `|md ... |` for a note with bold, italics or a list.

```d2
rule: |md
  **Rule:** one order per *basket*
|
```

**LaTeX label.** Use `|latex ... |` for a formula. The `**.style.font-size` line does not change its size, so check that the formula is readable in the PNG.

```d2
formula: |latex
  t = \frac{n}{r}
|
```

**Position.** `near` puts a shape at a fixed place, outside the layout. The values are `top-left`, `top-center`, `top-right`, `center-left`, `center-right`, `bottom-left`, `bottom-center` and `bottom-right`. A label can use `label.near`, with values such as `outside-top-left`. The title in SKILL.md uses `near: top-left`.

## Icons

Add `icon:` with a URL to show a picture in a box. Use `shape: image` to show only the picture and its label.

```d2
storage: Storage {icon: https://icons.terrastruct.com/essentials/112-server.svg}
server: Server {shape: image; icon: https://icons.terrastruct.com/essentials/112-server.svg}
```

- Before you use an icon URL, check that it answers 200: `curl -s -o /dev/null -w '%{http_code}' <url>`. Many paths on `icons.terrastruct.com` answer 403.
- Render a diagram with icons with `--bundle=false`. D2 0.8.1 embeds some icons as broken data, and the PNG then shows a broken-image mark.
- Use an icon only where it helps the reader find a part. An icon does not replace the label.

## Links and tooltips

`tooltip:` shows a text when the reader points at the box. `link:` opens a URL when the reader clicks the box. D2 draws a small mark on the box for each.

```d2
api: Orders API {tooltip: Checks the basket; link: https://example.com/orders}
```

Both work only in the SVG. A PNG shows only the marks. Put a fact that the reader needs into the label, not into a tooltip.

## Globs

A glob sets a value on many shapes or edges at once. `*` matches the shapes of one level. `**` matches the shapes of all levels. `(** -> **)[*]` matches all one-way edges. The house style block uses globs for the edge colour and the text size.

```d2
*.style.border-radius: 8
(** -> **)[*].style.stroke: "#57606A"
**: {&type: shape; style.font-size: 28}
```

A filter that starts with `&` limits a glob, such as `&shape: cylinder`. A later value on one shape replaces the glob value for that shape.

## Variables

Use `vars` for a value that repeats, such as a colour or a name. Use it as `${name}`.

```d2
vars: {
  core-fill: "#DDF0E4"
}
api: Orders API {style.fill: ${core-fill}}
```

Do not put `d2-config` into `vars`. It breaks the `**.style.font-size` line of the house style with the error `"style" needs a value`. `d2 validate` does not find this error. Give the layout, the theme and the sketch style as flags of the render command.

## Reserved names

Do not name a shape after a D2 keyword, such as `icon`, `label`, `style`, `shape`, `link`, `near`, `layers`, `scenarios` or `steps`. The errors do not name the cause. Examples are `reserved keywords are prohibited in edges` and `steps must be declared at a board root scope`. Give the shape another key, and keep the word in its label: `storage: Icon store`.
