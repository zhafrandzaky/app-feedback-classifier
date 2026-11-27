---
name: ui-ux-professional
description: Professional UI and UX rules for the Part 2 app (web or mobile) that classifies app reviews, covering layout, design tokens, icons (Lucide or custom SVG, never emoji), Indonesian copy, states, accessibility, and data display. Use when designing or building any screen, component, or chart in app/.
---

# Professional UI and UX

The Part 2 app is a working tool for people who read user feedback: a product team triaging reviews of e-wallet apps. It should feel calm, precise, and trustworthy, like a well-made analytics product, not a marketing page. Use this skill together with `frontend-design` for visual direction. Where they disagree, this skill and `CLAUDE.md` win.

## Hard rules

- No emoji anywhere in the interface, including empty states, toasts, buttons, and labels.
- Icons come from one library only: Lucide (`lucide-react`, `lucide-react-native`, or `lucide_icons` for Flutter), or custom SVG drawn on the same 24 px grid with a 1.5 to 2 px stroke. Do not mix icon sets.
- Icons support text, they do not replace it. An icon-only button needs an accessible label (`aria-label`, `accessibilityLabel`, or `Semantics`).
- Interface copy is formal, friendly Indonesian in sentence case. Label names are always the five official labels, spelled exactly.
- TypeScript in strict mode for Next.js and React Native.

## Screens to design first

1. Klasifikasi teks: a text area, a primary button "Klasifikasikan", and a result panel showing the predicted label, confidence for all five labels as a horizontal bar list, and the words that influenced the prediction highlighted in the original text.
2. Unggah CSV: file picker with format hint (`text` column required), progress, a summary of label distribution, a sortable table of results, and an export button.
3. Ringkasan: distribution per label and per app (DANA, GoPay, OVO, ShopeePay), trend over time when dates exist.
4. Tentang model: dataset size, labels and definitions, metrics on the test set, limitations.

## Design tokens

Define tokens once and use them everywhere. Never hard-code colors or spacing in components.

- Spacing scale: 4, 8, 12, 16, 24, 32, 48, 64.
- Radius: one small value for inputs and chips, one larger value for panels. Not everything gets the same radius.
- Type scale: body 16 px (14 px minimum for dense tables), line height 1.5 for body, 1.2 for headings. One or two typefaces. Tabular numerals for metrics.
- Color: a neutral base with one brand accent for primary actions and focus. Five categorical label colors, distinguishable for common color vision deficiencies and checked against both light and dark backgrounds. Status colors (success, warning, danger) are separate from label colors.
- Provide light and dark themes from the same token names.

Suggested label-to-icon mapping (Lucide names):

| Label | Icon |
|---|---|
| Permintaan Fitur | `lightbulb` or `square-plus` |
| Bug Aplikasi | `bug` |
| Desain Antarmuka | `layout-dashboard` or `palette` |
| Performa Aplikasi | `gauge` |
| Keamanan Aplikasi | `shield-check` |

Use the same color and icon for a label on every screen and chart.

## Layout

- Web: content max width around 1200 px, left-aligned text, a clear primary action per screen. Sidebar or top navigation with at most five destinations.
- Mobile: bottom navigation for top-level destinations, 44 x 44 pt minimum touch targets, primary action reachable by thumb.
- Responsive down to 360 px width with no horizontal page scroll. Tables become stacked cards or scroll inside their own container on small screens.

## States

Every data view needs all of these, designed, not left default:

- Empty: what this view will show and the action that fills it ("Tempel ulasan untuk melihat kategorinya").
- Loading: skeletons that match the final layout. Show progress for CSV processing.
- Error: what happened and how to fix it, in plain words ("File harus berformat CSV dan memiliki kolom text"). Errors do not apologize and are never vague.
- Success: confirm with the same verb as the action ("Klasifikasikan" results in "Selesai diklasifikasikan").
- Low confidence: when the top probability is below a threshold, say the model is unsure and show the top two labels.

## Data display

- Bar charts for label distribution, sorted by value. Avoid pie charts with more than three slices.
- Always show numbers next to bars or in tooltips, with percentages to one decimal.
- Confidence is shown as a percentage and a bar, never as a raw logit.
- Tables: right-align numbers, sticky header, sortable columns, truncated text with full text on expand.

## Accessibility

- WCAG 2.2 AA contrast: 4.5:1 for body text, 3:1 for large text and UI components.
- Visible focus ring on every interactive element. Full keyboard navigation on web.
- Every input has a visible label. Placeholder text is not a label.
- Respect reduced motion. Motion only to show what changed (result panel appearing, row expanding).
- Color is never the only carrier of meaning: label chips have text and icon as well as color.

## Copy guide

- Name things by what users understand: "Kategori", "Tingkat keyakinan", "Ulasan", not "class", "softmax", "payload".
- Buttons are verbs: "Klasifikasikan", "Unggah file", "Unduh hasil".
- Keep the vocabulary consistent across screens.

## Review checklist before shipping a screen

- [ ] No emoji, one icon set, icon buttons labelled.
- [ ] Tokens used, no hard-coded colors.
- [ ] Empty, loading, error, success states present.
- [ ] Works at 360 px and at desktop width, light and dark.
- [ ] Contrast and focus checked.
- [ ] Labels spelled exactly as the five official labels, with consistent colors and icons.
