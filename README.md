# CONVO-FILE-SPEC

A file format for documenting and sharing conversations🌟. The file extension is undefined.

The project name is also not confirmed for sure.

## Syntax

A text file composed of two parts, in order:
1. Content Block
2. Metadata Block

A separator must consist of four or more consecutive dash (-) characters follow by the content, with at least one blank line separating the content and the separator.

## Content Block

The Content Block contains the full text of the conversation.

* It must be a valid markdown ([CommonMark >=0.31.2](https://spec.commonmark.org/0.31.2/))
* Speaker turns MUST be denoted using a Speaker Delimiter, which consists of a Level 3 Markdown header (###), a single space, an at-symbol (@), and the participant's name (e.g., ### @Your Name).
* All text following a Speaker Delimiter, up to the next Speaker Delimiter or the Separator, is considered the content of that speaker's turn.
* To literally include the sequence ### @ in a conversation, it MUST be escaped with a backslash: \#\#\# @.

## Metadata Block

The Metadata Block provides machine-readable context about the conversation.

* It must be a valid single JSON object.
* The metadata object must be the last content in the file.

<table>
  <tr>
    <th>Key</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>type</td>
    <td><code>string/required</code> The nature of the conversation. "dialog" is generally used for a two-participant conversation, especially for AI chatbot. "conversation" is a catch-all reserved value if it is not sure.</td>
  </tr>
  <tr>
    <td>time</td>
    <td><code>string/required</code> The date or time in **ISO 8601** format, at minimum precise to the date. It is highly recommended to be read by `Temporal.ZonedDateTime.from()` with time zone information. This time is not defined as either the conversation start time or the publication time. However, if the conversation is updated later, this value should not be modified.</td>
  </tr>
  <tr>
    <td>participants</td>
    <td><code>Array/required</code></td> An array of participant following Participant Schema. The set of names in this array MUST exactly match the set of names used in the Content Block's Speaker Delimiters.
  </tr>
  <tr>
    <td>languages</td>
    <td><code>Array: string</code></td> An array of languages used in the conversation. If an contributor doesn't provide the language file, an speculative value might be provided (experimental).
  </tr>
</table>

To be or not to be creative to expand the schema is uncertain.

### Participant Schema

An item in the `participants` array can be one of two types:

A) Simple (String): A simple string representing the participant's name.

```json
"me"
```

B) Rich (Object): An object providing detailed metadata about the participant. `name` is the only required and reserved field.
* `name`: `string/required` The participant's name.
* `generative`: `boolean` `true` if the participant is a generative AI. Defaults to `false`.
* `generative:model`: `string` The proper model ID string for this (e.g., `gpt-5-2025-08-07` or `gemini-2.5-flash`) if `generative` is `true`.

### Language metadata (notes about speculative values)

This spec allows implementations to include optional language-related metadata (for example, `languages` and `languages_meta`). One special note about `languages_meta.speculative`:

- `languages_meta.speculative` is intended for machine-inferred, unverified results (for example produced by an automatic language-detector or model). It is *not* intended to be authored manually by end-users when they publish or upload conversation files via user-facing APIs.
- Platform implementers MAY populate `languages_meta.speculative` server-side as a convenience (for example to auto-fill a suggested primary language), but authors should not rely on or intentionally set this field in files they produce.
- In other words: `languages` is the canonical, author-declared list of languages; `languages_meta.speculative` is an implementation hint for inferred (unverified) values and should be treated as such by clients and UIs (show as “speculative” or similar).

Example (metadata snippet):

```json
{
  "languages": ["cmn", "en"],
  "languages_meta": {
    "speculative": ["en"],
  }
}
```

## Example file

```text
### @founder
Hi who are you.

### @Gem
I am Gemini 2.5 Pro. Nice to meet you.

### @founder
I am working on a project to create a website to archive some conversations and make it indexable for search engines. How do you think?

### @Gem
Sounds good.

----
{
  "type": "dialog",
  "time": "2025-10-23T12:00:00-05:00[America/Chicago]",
  "participants": [
    "founder",
    {
      "name": "Gem",
      "generative": true,
      "generative:model": "gemini-2.5-pro",
      "generative:interface": "https://gemini.google.com/app"
    }
  ]
}
```

## Notes

* I don't expect the file to be more than 10 MB🫣
* <code>v0.1.1</code>
