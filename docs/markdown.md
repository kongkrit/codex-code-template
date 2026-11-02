---
paths:
  - "**/README.md"
  - "docs/**/*.md"
---
# Markdown documents

These rules are for documents people read: the files matched by `paths` above. A long `##` section folds under its heading and a short one stays open, so a reader sees the title, the introduction and the headings, and opens only what they need. A folded section has exactly this shape:

```
## Installation

<details>
<summary>Requirements, install, first run</summary>

### Requirements

...

</details>
```

When it doesn't start with a `###` or `####` heading, add `<br>` on the line straight after `</summary>`:

```
## Troubleshooting

<details>
<summary>Common errors and their fixes</summary>
<br>

If the build fails with ...

</details>
```

- **Fold a `##` section of 8 lines or more; leave a shorter one open.** Count from the `##` line to the line before the next `##`, blank lines included. The last section has no next `##`: count to the end of the file, and fold it too if it reaches 8. Count the section as it reads unfolded, without the `<details>`, `<summary>`, `<br>` and `</details>` lines and the blank lines around them; folding adds 4 to 6 lines, which would otherwise push a short section past 8. Example: a heading, a blank line, a paragraph, a blank line, two list items and a blank line make 7 lines, so the section stays open.
- **A folded section has `<details>` and a one-line `<summary>` straight after its `##`.** Keep the `##` outside, as plain Markdown, never inside the `<summary>`, so the renderer (the program that turns Markdown into the page you see, such as GitHub or an editor's preview) still gives it an anchor (`#installation`) that other files can link to.
- **The summary says what the section holds**, usually its subsections or topics as a short list, such as "Installation, usage, how it works". Not "expand" or "click to show": the browser's ▸ arrow already says that. One line, no full stop.
- **`<br>` after `</summary>` unless a `###` or `####` follows.** GitHub's stylesheet puts no space above a paragraph, list or code block, so without it the text sits about 4 px under the summary; a `###` or `####` brings its own space (about 25 px), and the `<br>` matches it (about 27 px).
- **Blank lines:** one after `</summary>` (after the `<br>` when there is one) and one before `</details>`. The renderer reads the lines straight after an HTML tag as HTML, so without the first blank line a `###` shows up as literal text.
- **`</details>` closes the section** after its last line, before the next `##`.
- **Never fold the `#` title or the introduction under it.**
