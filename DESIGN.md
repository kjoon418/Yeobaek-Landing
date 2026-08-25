# Design

## Source of truth

- Status: Active
- Last refreshed: 2026-08-25
- Primary product surfaces: Kotlin Multiplatform mobile app reader, mobile web comment-unit interview prototype
- Evidence reviewed:
  - `docs/PRD.md`
  - `android/shared/src/commonMain/kotlin/com/yeobaek/feature/reader/ReaderScreen.kt`
  - `android/shared/src/commonMain/kotlin/com/yeobaek/feature/reader/component/ReaderTopBar.kt`
  - `android/shared/src/commonMain/kotlin/com/yeobaek/feature/reader/component/PassageItem.kt`
  - `android/shared/src/commonMain/kotlin/com/yeobaek/feature/reader/component/PassageCommentBottomSheet.kt`
  - `android/shared/src/commonMain/kotlin/com/yeobaek/feature/reader/component/PassageQuote.kt`
  - `android/shared/src/commonMain/kotlin/com/yeobaek/feature/reader/component/CommentItem.kt`
  - `android/shared/src/commonMain/kotlin/com/yeobaek/feature/reader/component/PassageCommentInput.kt`
  - `android/shared/src/commonMain/kotlin/com/yeobaek/core/designsystem/theme/Color.kt`
  - `android/shared/src/commonMain/kotlin/com/yeobaek/core/designsystem/theme/Type.kt`
  - `android/shared/src/commonMain/kotlin/com/yeobaek/core/designsystem/theme/Theme.kt`
  - `C:/Users/user/Desktop/번역/이상한_나라의_앨리스/ingest.json`

## Brand

- Personality: quiet, literary, warm, unhurried, and intimate.
- Trust signals: real book text, familiar reading rhythm, restrained controls, clear ownership of editable comments.
- Avoid: dashboard aesthetics, loud gradients, gamification, dense research instructions, and controls that compete with reading.

## Product goals

- Goals:
  - Let experienced exchange-reading participants compare five comment attachment interactions on a smartphone.
  - Reproduce the moment of reading, discovering existing comments, and leaving a comment.
  - Keep content and existing comment meaning constant so the interaction unit is the primary variable.
- Non-goals:
  - Collect interview answers or analytics.
  - Persist data to a server.
  - Reproduce club creation, joining, account, or management flows.
  - Optimize a separate desktop experience.
- Success signals:
  - All five experiences are reachable in the agreed order and can be revisited.
  - Existing, overlapping, and newly authored comments can be viewed and managed without losing the reading context.
  - Core interactions work in mobile Chromium and WebKit emulation before Vercel deployment.

## Personas and jobs

- Primary personas: people who already participate in exchange-reading or book-club discussions.
- User jobs:
  - Read a realistic excerpt.
  - Notice where other readers commented.
  - Open an existing comment, including overlaps.
  - Attach a new comment using each unit-selection method.
  - Edit or delete only a comment they authored.
- Key contexts of use: moderated interview on the participant's smartphone, with interview notes recorded outside the page.

## Information architecture

- Primary navigation: start screen -> five ordered reader experiences with adjacent previous/next navigation -> completion screen -> restart.
- Core routes/screens:
  - Start: short title, one-line explanation, `체험 시작`.
  - Reader: experience label, step indicator, reader, contextual selection action, comment sheets, previous/next.
  - Complete: short completion message, `처음부터 다시 체험`.
- Content hierarchy: experience identity -> book title/author -> book text -> comment annotations -> contextual action -> comment sheet.

## Design principles

- Reading stays primary: guidance and experiment controls remain compact and outside the prose rhythm.
- Change one interaction variable at a time: text, existing comment meaning, visual language, and sheet structure remain constant.
- Make overlap legible: annotated text uses only an underline; intersecting ranges use a darker double underline and open a range chooser before comments.
- Preserve direct manipulation: sentence tap and browser-native text selection remain the main gestures; secondary actions appear beside the chosen range.
- Tradeoffs: research clarity and implementation size take priority over future extensibility or a general-purpose annotation model.

## Visual language

- Color:
  - Paper `#FAF7F2`
  - Ink/text `#3C3833`
  - Accent `#B59A7D`
  - Surface `#FFFFFF`
  - Soft surface `#F5F0E8`
  - Base annotation underline uses the accent family; overlapping ranges use a darker double underline without any background fill
  - Secondary text `#6B645E`, muted text `#8C857D`
  - Line `#EEE7DE`, outline `#D8CEC3`, error `#C0492F`
- Typography:
  - Ridi Batang for book text and reader title.
  - Wanted Sans for controls, labels, comments, and instructions.
  - Reader text targets 17-18px with approximately 2x line height and restrained letter spacing.
- Spacing/layout rhythm:
  - Reader side gutters: 32px-equivalent.
  - Passage gap: 28px-equivalent.
  - Sheet side gutters: 24px-equivalent.
- Shape/radius/elevation:
  - Comment sheets use a white surface with 20px rounded top corners, a subtle scrim, and a 36x4px drag handle.
  - Buttons and fields use soft pill or large rounded shapes.
- Motion: short opacity/translation transitions only; no decorative motion.
- Imagery/iconography: use the existing minimal line-icon language; no book cover or decorative illustration is required.

## Components

- Existing components to reuse as visual references:
  - Reader scaffold and passage rhythm.
  - Centered reader top bar.
  - Passage quote, comment list/item, comment input, action menu, delete confirmation, and modal bottom sheet.
- New/changed components:
  - Start and completion screens.
  - Experience header and previous/next controls.
  - One-time tutorial overlay per experience.
  - Sentence-tap range selector.
  - Native-selection observer and contextual `댓글 달기` action.
  - Comment range annotation compositor with solid and double underline variants.
  - Short range-choice bottom sheet.
  - For `여러 문장—탭`, one tap opens comments and a roughly 550 ms long-press starts sentence-range selection. Moving, scrolling, or cancelling the pointer aborts the long-press without blocking vertical scrolling.
  - Comment ranges use ordered start/end endpoints (`startPassage`, `startOffset`, `endPassage`, `endOffset`) so sentence-tap, sentence-drag, and word selection can cross paragraph boundaries. Existing session data using one-paragraph ranges is normalized when read.
- Variants and states:
  - `현재 문단 방식`: tapping a paragraph opens its comments/input.
  - `여러 문장—탭`: tap once to view comments (including the existing overlap range chooser), or long-press any sentence to set the range start. Tap the end sentence in either direction, including across paragraphs, then use contextual `댓글 달기`. Keyboard Enter/Space remains comment viewing rather than range selection.
  - `여러 문장—드래그`: long-press/drag across one or more paragraphs, normalize the visible native selection to whole-sentence bounds, then use contextual `댓글 달기`.
  - `한 문장`: one tap opens the sentence's comments/input.
  - `여러 단어`: long-press/drag preserves the selected word range, then use contextual `댓글 달기`.
- Token/component ownership: mobile app theme files remain canonical for brand tokens; web-only interaction tokens live with the prototype CSS.

## Accessibility

- Target standard: practical WCAG 2.2 AA for text, controls, focus, and dialogs.
- Keyboard/focus behavior: dialog focus is contained, Escape dismisses non-destructive overlays, and focus returns to the invoking control where applicable.
- Contrast/readability: annotation underlines must remain visible without obscuring glyphs; overlap also changes line style so it does not rely on color alone.
- Screen-reader semantics: experience state, buttons, dialog titles, comment ownership, and editable actions use explicit labels.
- Reduced motion and sensory considerations: honor `prefers-reduced-motion`; do not rely on color alone for the active selection or ownership.

## Responsive behavior

- Supported breakpoints/devices: smartphone portrait layouts from approximately 320px to 430px; iPhone Safari and Android Chrome are the compatibility targets.
- Layout adaptations: on wider viewports, keep a centered phone-width reader canvas rather than creating a desktop IA.
- Touch/hover differences: touch is primary; hover is non-essential. Native browser selection handles remain browser-controlled. Native callout is disabled only on `여러 문장—탭` sentences, whose pointer gesture keeps vertical pan available.

## Interaction states

- Loading: none after the static document loads.
- Empty: comment sheet explains that no comments exist yet while preserving the input.
- Error: invalid/blank comment submission is disabled; destructive action uses confirmation.
- Success: newly authored or edited comments appear immediately in their experience-specific state.
- Disabled: selection action is hidden until a valid range exists; previous/next reflects the first/last step.
- Offline/slow network: all content and fonts are local static assets after the initial page load.

## Content voice

- Tone: concise, neutral, conversational Korean that assumes book-club familiarity.
- Terminology: use the explicit labels `현재 문단 방식`, `여러 문장—탭`, `여러 문장—드래그`, `한 문장`, `여러 단어`.
- Microcopy rules: tutorial overlays state only the gesture and next action; do not explain hypotheses, advantages, or disadvantages during the experience.

## Implementation constraints

- Framework/styling system: plain HTML, CSS, and JavaScript; no package manifest, framework, analytics, or runtime dependency.
- Design-token constraints: derive web variables from the existing mobile theme rather than adding a separate design system.
- Performance constraints: static assets only; avoid layout work proportional to anything beyond the ten passages and current annotations.
- Compatibility constraints: mobile Safari and Chrome, native Selection API for drag modes, cross-paragraph ordered ranges, and session-scoped state that survives reload but resets when the tab session ends.
- Test/screenshot expectations: validate logic directly, then exercise every flow at representative mobile Chromium and WebKit viewports and capture screenshots for visual review.

## Open questions

- None. New contradictions found during implementation must be added here before changing interaction behavior.
