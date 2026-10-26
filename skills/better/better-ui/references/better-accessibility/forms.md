# Forms

Labels, error messaging, autocomplete, input types, WCAG 2.2 form criteria and submit
behavior. Label and error wording belong to [better-writing](../better-writing.md).

## Labels

Every control needs a programmatic label: `<label for>` pointing at the input's `id`,
or a wrapping `<label>`. A placeholder disappears the moment the user types and
usually fails contrast, so it never stands in for a label. Used in addition to a
label, it can show the expected format: `placeholder="name@company.com"`.

```html
<!-- Good: explicit association -->
<label for="email">Email</label>
<input id="email" type="email" autocomplete="email" />

<!-- Good: wrapping label, so label and control share one hit target -->
<label>
  <input type="checkbox" /> Send me updates
</label>
```

Clicking "Send me updates" toggles the checkbox, with no dead zone between them. Mark
required fields with native `required` plus a visible indicator explained once per
form ("* required"). Group related radios and checkboxes in a `<fieldset>` whose
`<legend>` asks the question.

## Error messaging

The complete pattern:

```html
<label for="email">Email</label>
<input
  id="email"
  type="email"
  autocomplete="email"
  aria-invalid="true"
  aria-describedby="email-error"
/>
<p id="email-error">Enter a valid email address.</p>
```

- `aria-invalid="true"` on the failing field, removed once fixed.
- `aria-describedby` links the field to its inline error so screen readers announce
  it with the field.
- Errors render inline beside their fields, with an icon or text. Never a red border
  alone, which is a color-only cue.
- On submit, focus the first invalid field.
- Accept free text and validate after. Never block typing or filter characters as the
  user types. Trim values before validating, because autocomplete and text expansion
  add trailing spaces.

## Autocomplete and input types

A valid `autocomplete` token on fields about the user is a WCAG requirement (1.3.5).
Pair it with a descriptive `name` attribute, a real `<form>` and no fake inputs, so
password managers and 2FA autofill work. The common tokens:

| Field | `autocomplete` |
| --- | --- |
| Name | `name` (or `given-name` / `family-name`) |
| Email | `email` |
| Phone | `tel` |
| Address | `street-address`, `address-line1`, `postal-code`, `country` |
| Card | `cc-number`, `cc-exp`, `cc-csc`, `cc-name` |
| Login | `username`, `current-password` |
| Signup / reset | `username`, `new-password` |
| 2FA code | `one-time-code` |

Prefix with a section where relevant: `autocomplete="shipping street-address"`.

Correct `type` and `inputmode` pick the right mobile keyboard:

| Input | Use |
| --- | --- |
| Email, URL, phone | `type="email"`, `type="url"`, `type="tel"` |
| OTP / PIN / card number | `type="text" inputmode="numeric"` (keeps text semantics, no spinner) |
| Money, decimals | `type="text" inputmode="decimal"` |
| True numeric quantity | `type="number"` |

Disable spellcheck on emails, codes and usernames: `spellcheck="false"`.

## WCAG 2.2 form criteria

- **Accessible authentication** (3.3.8). Login never depends on memorizing,
  transcribing or solving a puzzle without an alternative. Allow paste and password
  managers in every credential and code field, and offer a non-cognitive path beside
  any CAPTCHA.
- **Redundant entry** (3.3.7). Never ask for the same information twice in one
  process. Prefill it, or offer a choice such as "Same as shipping address".
- **Consistent help** (3.2.6). Where a flow offers help, such as a contact link or
  chat, keep it in the same relative place on every step.

## Submit behavior

- Keep submit enabled until the request starts. While it runs, show a spinner *beside
  the original label*: "Save" with a spinner, not a bare spinner. The label is what
  tells assistive tech which button is busy.
- Mark the pending button `aria-disabled="true"` and ignore repeat clicks in the
  handler. Native `disabled` on the focused button drops focus to `<body>`.
- Announce results. Success goes through a polite live region. On failure, focus the
  first invalid field, which is itself the announcement. Reserve `role="alert"` for
  form-level errors not tied to a field ([screen-readers.md](screen-readers.md)).
- Warn on unsaved changes before navigation. A re-render or hydration must never
  reset a field's typed value or move focus out of it.
