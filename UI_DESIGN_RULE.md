# Voice Studio — UI Design Rules

## Design Direction

Create a modern desktop application inspired by the clarity and restraint of Google's Material 3 design language.

Do not copy Google products or proprietary UI designs.

The product should feel:

* Clean
* Calm
* Premium
* Friendly
* Technical without being intimidating
* Spacious
* Fast
* Professional

Avoid making the application look like traditional audio software.

## Platform

Primary platforms:

* Windows
* macOS

Use Compose Multiplatform/Desktop.

The interface must work well with:

* Mouse
* Trackpad
* Keyboard
* Window resizing
* High-DPI displays

## Layout

Use a persistent left navigation rail/sidebar.

Recommended structure:

Home
Projects
Record
Enhance
Settings

Keep navigation minimal.

The main content area should have generous spacing.

Prefer a maximum content width for focused workflows rather than stretching every element across the entire window.

## Visual Language

Use:

* Rounded cards
* Soft surfaces
* Clear hierarchy
* Large readable typography
* Subtle borders
* Minimal elevation
* Consistent spacing
* Material 3-inspired components

Avoid:

* Excessive gradients
* Excessive shadows
* Glossy UI
* Tiny controls
* Dense toolbars
* Visual clutter
* Excessive animation

## Color

Use a restrained neutral foundation.

Support:

* Light theme
* Dark theme

Use one primary accent color.

Do not assign arbitrary colors to every control.

Audio states may use semantic colors where appropriate:

* Recording
* Processing
* Success
* Warning
* Error

## Typography

Prioritize readability.

Use a clear modern sans-serif typeface.

Use strong hierarchy:

Display
Headline
Title
Body
Label

Do not use excessive font weights.

## Audio Visualization

Waveforms should be visually elegant and readable.

Do not create unnecessarily complex visualizations.

The waveform should communicate:

* Recording activity
* Audio amplitude
* Processing state
* Selection

Animations should remain smooth and lightweight.

## Primary Actions

Every screen should have one visually dominant primary action.

Examples:

Record:
"Start Recording"

Enhance:
"Enhance Voice"

Export:
"Export Audio"

Do not make every button visually prominent.

## Enhancement UX

The most important interaction is comparing original and enhanced audio.

Provide an obvious:

Original ↔ Enhanced

A/B comparison control.

Users should be able to preview changes before exporting.

## Presets

Provide simple presets:

Natural
Studio
Podcast
Voice Over
Clean Speech

Presets should be understandable to non-technical users.

Advanced controls may exist, but they should not dominate the main interface.

## Progressive Disclosure

Hide advanced audio controls until the user asks for them.

Basic experience:

* Noise Reduction
* Voice Clarity
* Room Echo
* Warmth

Advanced experience may expose:

* EQ
* Compressor
* Limiter
* Gate
* De-esser
* Gain
* Loudness

## Animation

Use subtle animations for:

* Recording state
* Processing progress
* Page transitions
* Waveform updates
* A/B switching

Never block interaction because of animation.

Avoid excessive motion.

## Empty States

Empty states should be helpful rather than decorative.

Example:

"No recordings yet"

"Record your first voice clip to get started."

Primary action:
"Start Recording"

## Error States

Errors must explain:

1. What happened.
2. Why it happened when known.
3. What the user can do next.

Avoid technical error messages unless the user opens diagnostic details.

## Accessibility

Support:

* Keyboard navigation
* Visible focus states
* Sufficient contrast
* Screen-reader-friendly labels
* Resizable windows
* Large click targets

Never rely only on color to communicate state.

## AI Coding Rule

Before creating a new UI component:

1. Search for an existing component.
2. Reuse existing design tokens.
3. Follow existing spacing and typography.
4. Avoid creating one-off components.

Build a small reusable design system.

Suggested components:

AppShell
NavigationRail
TopBar
PrimaryButton
SecondaryButton
AudioWaveform
AudioPlayer
RecordButton
PresetCard
Slider
ProcessingIndicator
A/BCompare
ProjectCard
EmptyState
ErrorState

## Product Principle

The application should feel simple for a beginner but powerful for an advanced user.

Do not expose the complexity of the audio engine in the primary workflow.
