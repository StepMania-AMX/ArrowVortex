# Roadmap: Small, Focused Tasks

Purpose

Curated, incremental tasks aligned with the documented subsystems to make onboarding and contributions straightforward. Tasks are intentionally small in scope and reference concrete files to minimize ambiguity. This roadmap uses StepMania 5 terminology for familiarity.

Contributing guidance

- Prefer landing one focused change per PR.
- Add links to the docs where helpful: [Architecture overview](./architecture/overview.md), [Subsystems](./architecture/subsystems.md), [StepMania mapping](./architecture/stepmania-mapping.md), [Glossary](./glossary.md).
- When touching timing/notes, verify conversions via [Vortex::TimingData](../src/Simfile/TimingData.h#L10) and trackers [Vortex::TempoTimeTracker](../src/Simfile/TimingData.h#L48), [Vortex::TempoRowTracker](../src/Simfile/TimingData.h#L66).

Core

- Add High-DPI scaling controls in GUI (configurable scale factor). Touch points: [GuiContext](../src/Core/GuiContext.h), [Renderer](../src/Core/Renderer.h), [TextLayout](../src/Core/TextLayout.h).
- Add text rendering fallback path when a glyph is missing (report once, substitute). Touch points: [FontManager](../src/Core/FontManager.h), [TextDraw](../src/Core/TextDraw.h).
- Add a debug overlay toggle to show draw call counts and frame time. Touch points: [Debug](../src/System/Debug.h), [Renderer](../src/Core/Renderer.h).
- Document widget composition patterns in code comments in [Widgets](../src/Core/Widgets.h) and related files.

System

- Add log-level filtering and a rolling log file size cap. Touch points: [Debug](../src/System/Debug.h), [File](../src/System/File.h).
- Normalize path handling for non-Windows path separators on import. Touch points: [File](../src/System/File.h).
- Introduce a tiny task runner for background jobs (audio analysis, parsing) with cancellation. Touch points: [Thread](../src/System/Thread.h).

Simfile (data model and formats)

- Enforce BPM at row 0 on sanitize with an opt-out flag if needed. Touch points: [Vortex::Tempo::sanitize()](../src/Simfile/Tempo.h#L51), segments in [Segments](../src/Simfile/Segments.h).
- Add validation helper returning a structured report (missing offset, overlapping holds, unsorted notes). Touch points: [NoteList](../src/Simfile/NoteList.h), [Chart](../src/Simfile/Chart.h#L12).
- Unit-test encode/decode roundtrip for rows vs timestamps (timebase drift). Touch points: [NoteList::encode() / decode()](../src/Simfile/NoteList.h).
- Improve DWI import edge cases (rolls, mines) and annotate limitations inline. Touch points: [LoadDwi.cpp](../src/Simfile/LoadDwi.cpp).
- Verify SSC (SM5) DELAYS/WARPS interactions produce consistent [Vortex::TimingData](../src/Simfile/TimingData.h#L10) events. Touch points: [LoadSm.cpp](../src/Simfile/LoadSm.cpp), [Parsing](../src/Simfile/Parsing.h).

Managers

- Add safety checks and user feedback for switching split timing modes that would invalidate segments. Touch points: [Vortex::TempoMan](../src/Managers/TempoMan.h#L8).
- Implement clipboard format versioning tag and graceful degrade. Touch points: [TempoMan::clipboardTag](../src/Managers/TempoMan.h#L13).
- Add API to query effective BPM range excluding negative/warped rows for display. Touch points: [Vortex::TempoMan::getBpmRange()](../src/Managers/TempoMan.h#L116).
- Expose a read-only view of the current [SegmentGroup](../src/Simfile/Tempo.h#L57) for UI inspection while editing.
- Style discovery improvements: attach metadata such as panel geometry provenance to [Style](../src/Managers/StyleMan.h#L8).

Editor Interface

- Add an optional “live anchors” overlay (measure numbers, labels from [Vortex::Label](../src/Simfile/Segments.h#L183)) to [TempoBoxes](../src/Editor/TempoBoxes.h) and [Notefield](../src/Editor/Notefield.h).
- Add a performance mode toggle to reduce overdraw on [Minimap](../src/Editor/Minimap.h) and [Notefield](../src/Editor/Notefield.h).
- Provide configurable color themes, especially for Fakes/Lifts visualization, consistent with [Segments](../src/Simfile/Segments.h).

Editor Editing

- Add granular undo labels per action (e.g. “Insert 32nd notes”, “Mirror H”). Touch points: [Action](../src/Editor/Action.h), [History](../src/Editor/History.h).
- Improve selection marquee feedback when crossing players/columns. Touch points: [Selection](../src/Editor/Selection.h).
- Document and surface non-destructive paste (Shift+Ctrl+V) behavior in UI help. Touch points: [Shortcuts](../src/Editor/Shortcuts.h), docs.

Editor Audio and Analysis

- Cache downsampled waveforms per zoom level to speed scrubbing. Touch points: [Waveform](../src/Editor/Waveform.h).
- Add latency calibration tool that adjusts [Vortex::Tempo::offset](../src/Simfile/Tempo.h#L53).
- Make encoder detection/path for Ogg explicit in settings with a test button. Touch points: [ConvertToOgg](../src/Editor/ConvertToOgg.h).

Dialogs

- Add preview playback in Adjust Tempo dialog synchronized with [Vortex::TempoMan](../src/Managers/TempoMan.h#L8) tweak mode.
- Expand Custom Snaps dialog to show snap color legend and quant values (ties into [Vortex::ExpandedNote::quant](../src/Simfile/Notes.h#L57)).
- Tempo Breakdown dialog: add export-to-CSV (row, time, BPM, measure) using [Vortex::TimingData](../src/Simfile/TimingData.h#L10).

Quality of life and documentation

- Add a “First Issues” section in docs pointing to this roadmap and tagging a few tasks in the issue tracker.
- Add build instructions per VS version and dependencies in [README.md](../README.md) and link from [docs/README.md](./README.md).
- Add coding conventions (headers, namespaces, includes) in docs and apply to a small module as an example PR.

Testing ideas (incremental)

- Golden file roundtrips: SM/SSC load-save-load equivalence for a small corpus. Touch points: [LoadSm.cpp](../src/Simfile/LoadSm.cpp), [SaveSm.cpp](../src/Simfile/SaveSm.cpp).
- Timing conversions: assert monotonicity and invertibility of time/row/beat on synthetic segments. Touch points: [TimingData](../src/Simfile/TimingData.h#L10).
- NoteList conflict handling: craft overlapping hold/mines cases and assert [prepareEdit](../src/Simfile/NoteList.h#L35) results.

Nice-to-have ideas

- Theme-able note colors and per-style default mirrors via [Style](../src/Managers/StyleMan.h#L8).
- Shader hot-reload for rapid iteration during UI work. Touch points: [Shader](../src/Core/Shader.h).
- Export “tempo map only” as a utility file for DAW alignment using [Vortex::TimingData](../src/Simfile/TimingData.h#L10).

How to pick a task

1) Choose a subsystem above.
2) Open the referenced files.
3) Propose a focused change as a GitHub issue referencing this roadmap section and the file links.
4) Submit a PR with before/after notes or screenshots if UI.
