# Wrapping and punctuation

Line length, wrapping, justification, typographic characters and mixed-direction text.

## Measure (line length)

Any unit works. `65ch` measures characters directly, one `ch` being the width of the `0` in the current font, and a pixel or rem cap is just as good. At a `16px` body size the 60–75 character range lands roughly between `560px` and `680px` depending on the font. Tailwind's `max-w-xl` (`576px`), `max-w-2xl` (`672px`) and `max-w-prose` (`65ch`) all fit. Recheck the cap if the body font size changes.

## Wrapping

| Declaration | Use | Avoid |
| --- | --- | --- |
| `text-wrap: balance` | Headings, titles, short descriptions | Paragraphs; Chromium ignores it past six lines |
| `text-wrap: pretty` | Any paragraph, long-form included | Nothing; unsupported browsers wrap normally |
| `overflow-wrap: break-word` | Containers that can hold long words, URLs or IDs | — |
| `white-space: nowrap` | Labels, badges, values like `10 km` | Anything that can outgrow its container |

A balanced heading keeps its box at full width. Where a hugging box matters, such as a centered pill, set `width: fit-content` too.

## Justification

Interface text stays at `text-align: start`. Justified text belongs only in editorial layouts, and only with `hyphens: auto` and a correct `lang`, otherwise stretched spaces open rivers down the column. Narrow columns cannot be justified well even with hyphenation.

```css
.article-body {
  text-align: justify;
  hyphens: auto;
}
```

## Smart punctuation

| Instead of | Use |
| --- | --- |
| Straight quotes `"..."` | Curly quotes that curve around the text (keep straight quotes in code) |
| Hyphen in ranges | En dash: `2010–2020` |
| Two hyphens for an aside | Em dash character |
| Three periods `...` | The single ellipsis character `…` |
| `x` in dimensions | The multiplication sign: `1920×1080` |
| Regular space in `10 km` | `&nbsp;` so the value never breaks apart |
| Uncontrolled word breaks | `&shy;` to mark where a word may break, or `hyphens: auto` with the correct `lang` |

## Internationalization

- **Long paragraphs align by their own language.** A one- or two-line snippet follows the surrounding UI's direction. A paragraph of three or more lines aligns to its own script instead, so an English paragraph stays start-aligned LTR even inside an RTL interface. `text-align: start` with the correct `lang` and `dir` on the paragraph element handles this.
- **Digits keep their order.** A phone number or "541" reads identically in RTL, because the Unicode bidi algorithm keeps digit runs left to right. `<bdi>` repairs a value whose order adjacent RTL text disturbs.
- **Quotes follow the language.** `<q>` renders the quotation marks the element's `lang` expects, such as `«…»` in French.
