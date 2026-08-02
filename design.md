# Design notes — Lenny Growth Assistant frontend

## The brief, in one line

A workspace for querying a podcast archive and turning insight into publishable writing. It should
feel like a researcher's notebook next to a recording, not a generic chatbot skin.

## Palette

| Token | Hex | Use |
|---|---|---|
| `ink` | `#1C1B1A` | Sidebar, primary text, user bubbles |
| `paper` | `#FAF7F1` | Main canvas background |
| `paper-dim` | `#F1ECE1` | Secondary surfaces (composer chrome, code inline) |
| `paper-line` | `#E4DDCE` | Hairline borders |
| `brass` | `#C08A2E` | Primary accent — active skill, links to insight |
| `pine` | `#2F6B5E` | Secondary accent — artifact viewer, citations |
| `rust` | `#A9483A` | Reserved for destructive/error states |

Deliberately avoided the near-black-with-neon-accent and warm-cream-with-terracotta looks that AI
tools default to — brass and pine were picked because they read as "archive / tape / annotation"
rather than "AI product."

## Type

- **Fraunces** (serif, display) — session titles, empty-state headline, artifact titles. Gives the
  product a "written" register instead of a SaaS-dashboard one.
- **Inter** (sans) — all UI chrome and body copy, for legibility at small sizes.
- **IBM Plex Mono** (mono) — model names, citation timestamps, artifact source view. Signals "this
  is a precise, technical detail" wherever it appears.

## Layout concept

```
┌────────────┬───────────────────────────────┬─────────────────────┐
│  Sidebar    │        Chat (transcript)      │   Artifact Viewer   │
│  (ink bg)   │        (paper bg)             │   (opens on demand) │
│             │                                │                     │
│  + New chat │  user → right-aligned bubble   │  Preview / Source   │
│  sessions   │  assistant → left rule, no     │  toggle             │
│             │  bubble — reads like a         │  copy / download    │
│             │  transcript entry              │                     │
└────────────┴───────────────────────────────┴─────────────────────┘
```

The artifact panel is collapsed by default and slides in only when the model actually produces one
(a Ship30for30 essay, or HTML/Markdown asked for explicitly) — it never competes for space with the
conversation when there's nothing to show.

## Signature element

Assistant replies aren't chat bubbles. They're laid out like transcript entries: a small quote-mark
mark, a thin left rule, and — when the answer is grounded — citation chips styled like tape labels
(`episode name · timestamp`) under the text. This is the one place the design takes a real position:
it makes "this came from the podcast, here's exactly where" a first-class visual element instead of
a footnote, which is the actual product promise of a RAG-over-transcripts tool.

## Copy voice

Empty states and placeholders speak plainly about what the tool does ("Ask the show anything") and
what's missing ("Backend not connected yet — showing a mock response…") rather than hiding gaps
behind spinners or vague errors.

## Accessibility / quality floor

- Responsive down to a single-column layout on mobile widths (sidebar and artifact panel collapse
  behind the chat first).
- Visible focus states on all interactive elements (native browser focus ring preserved, not
  suppressed).
- Streaming text updates don't rely on color alone — a typing indicator (three dots) marks
  in-progress replies.
