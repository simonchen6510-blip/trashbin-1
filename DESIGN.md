# Ting Yu Ji Design Notes

## Product Character

Ting Yu Ji is a private rain-sound journal. The interface should feel quiet, tactile, and reflective: closer to opening a paper album than operating a dashboard.

Use external design systems as references for interaction quality, not visual identity:

- Apple HIG for recording, permission, and system-level feedback.
- Material Design for loading, error, and disabled states.
- GitHub Primer for component consistency and maintainable UI states.
- Notion-like restraint for content surfaces, without copying the brand.

## Visual Language

- Keep the main experience immediate. The first screen is the recording surface, not a landing page.
- Use paper white, ink gray, rain blue, and small green-gray accents.
- Avoid dominant purple-blue AI gradients, SaaS dashboard styling, marketing hero layouts, and card-heavy pages.
- Use texture sparingly. Paper texture belongs on album/result surfaces, not every panel.
- Prefer calm contrast and precise spacing over decorative effects.

## Components

- The record control may stay circular because it is the primary instrument, not a generic text button.
- Secondary actions should be compact text or icon buttons with stable hit areas.
- Repeated album items can be framed, but avoid large ornamental card shells around whole sections.
- Rounded rectangles should stay at 8px radius or less unless the shape is the record control or generated artwork.
- Use lucide icons for familiar tool actions.

## Interaction States

- Recording, generating, success, empty, permission denied, and network failure states must be visible in the UI.
- Errors should not only be logged to the console. Give the user a concise, actionable message.
- The Codex preview pane may not expose microphone access. In dev, provide both a local preview path and a Gemini-backed mock path, so visual QA and model integration QA can be tested separately.
- The test panel is development-only. It can be reachable with `?dev=true` or a keyboard shortcut in dev builds, but must not appear in production.
- Text must fit in the Codex preview pane, mobile widths, and desktop widths without negative letter spacing.

## Copy

- Keep copy short and human. Do not explain the interface inside the interface.
- Chinese UI text should feel like a journal, not enterprise software.
- Prefer direct verbs for actions: `收藏`, `返回`, `重试`.
