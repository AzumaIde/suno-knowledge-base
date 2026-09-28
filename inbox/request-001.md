# Import Request 001

## Status

`waiting-for-url`

## Target

Suno URL:

```text
PASTE_SUNO_URL_HERE
```

## Mission

Import exactly one Suno song into this repository according to `docs/architecture.md`.

Use `templates/song.md` as the starting schema, but adapt the Mermaid structure and analysis to the actual song. Do not force the song into a predefined structure if the evidence suggests another form.

## Required source collection

From the specified Suno page, collect if available:

- title
- creator name
- creator/profile URL
- song URL
- caption
- style text exactly as published
- lyrics exactly as published
- other clearly relevant public metadata

If public audio can be played or inspected, analyze the audible song as well.

Do not invent unavailable information.

## Required analysis

### Lyrics

- Create readable Clean Lyrics without rewriting the words
- Remove non-sung production/directive annotations from Clean Lyrics while preserving them separately
- Repair excessive one-word-per-line formatting where meaning and phrasing clearly support grouping
- Identify central themes
- Extract core keywords
- Identify symbols / motifs
- Explain perspective, repetition, contrast, and semantic development

### Structure

Infer the song's actual structural logic.

Possible patterns include but are not limited to:

- parallel evolution across verse 1 / verse 2 / final chorus
- spiral repetition
- linear narrative
- dual-layer structure
- contrast / reversal
- hybrid

Generate at least one Mermaid diagram that visually represents the meaningful relationships. A vertical flowchart is not mandatory. Prefer side-by-side or cross-linked structures where the song is built from corresponding sections.

### Audio

If audio is accessible, describe reusable musical observations such as:

- perceived tempo
- instrumentation
- vocal character
- energy curve
- density changes
- breaks / stops
- transitions
- climax
- notable production choices

Clearly mark these as audio-derived observations rather than source-page facts.

### Suno knowledge

Preserve:

- Style raw text verbatim
- Directive raw text verbatim
- where directives occur
- observed audible effect when reasonably identifiable

Do not replace the original Style with an AI-inferred Style.

## Az Impression

Do not invent Az's personal reaction.

Create the section, but if Az has not supplied a reaction, write:

> No Az note yet.

The Chat side will add or structure Az's impressions later.

## Output

Create one Markdown file at:

```text
songs/<creator-slug>/<song-slug>.md
```

After successful import, update the status in this request to `processed` if practical, and add the generated file path.

## Quality bar

The resulting Markdown should contain enough information that a later Chat session can discuss the song, compare it with other stored songs, and extract composition knowledge without needing to reopen the Suno page for ordinary analysis.
