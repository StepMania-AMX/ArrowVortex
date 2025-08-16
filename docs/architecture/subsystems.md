# Subsystems Deep Dive

Purpose

This document groups ArrowVortex into major subsystems, explains responsibilities and boundaries, and references analogous StepMania 5 components for quick orientation.

Conventions for links

- Filenames are linked directly to their repository path.
- Language constructs are linked to their declaration location using [namespace::Type or Function()](relative/path:line) form.

Top-level subsystem map

- Core
- System
- Simfile (data model and formats)
- Managers (stateful controllers)
- Editor Interface (views and UI surfaces)
- Editor Editing (selection, actions, history, tools)
- Editor Audio and Analysis
- Dialogs (feature workflows)

Core

- Role: foundational utilities, math/containers, rendering, text, and in-house GUI toolkit.
- Key utilities: [Vortex::String](../../src/Core/String.h), [Vortex::Vector](../../src/Core/Vector.h), [Vortex::ByteStream](../../src/Core/ByteStream.h), [Vortex::NonCopyable](../../src/Core/NonCopyable.h)
- Rendering: [Vortex::Renderer](../../src/Core/Renderer.h), [Vortex::Texture](../../src/Core/Texture.h), [Vortex::ImageLoader](../../src/Core/ImageLoader.h), [Vortex::TextLayout](../../src/Core/TextLayout.h), [Vortex::Text](../../src/Core/Text.h), [Vortex::Shader](../../src/Core/Shader.h), [Vortex::QuadBatch](../../src/Core/QuadBatch.h)
- GUI: [Vortex::GuiContext](../../src/Core/GuiContext.h), [Vortex::GuiManager](../../src/Core/GuiManager.h), [Vortex::GuiWidget](../../src/Core/GuiWidget.h), [Widgets API](../../src/Core/Widgets.h), plus widget parts in [WidgetsScroll](../../src/Core/WidgetsScroll.cpp), [WidgetsSelect](../../src/Core/WidgetsSelect.cpp), [WidgetsSimple](../../src/Core/WidgetsSimple.cpp), [WidgetsText](../../src/Core/WidgetsText.cpp)
- SM5 analogs: closest to theming/rendering primitives and low-level draw pipeline; not a direct match (StepMania uses Actor/Theme metrics), but conceptually underpins NoteField and screens.

System

- Role: OS abstractions (files, threads), OpenGL bootstrap, debugging/logs, core mixer.
- Files: [Vortex::System](../../src/System/System.h), [Vortex::File](../../src/System/File.h), [Vortex::Thread](../../src/System/Thread.h), [Vortex::Mixer](../../src/System/Mixer.h), [OpenGL](../../src/System/OpenGL.h), [Debug](../../src/System/Debug.h), resources via [Resources](../../src/System/Resources.h)
- SM5 analogs: rough equivalents to RageFile, threading wrappers, GL initialization, and sound backends.

Simfile (data model and formats)

- Role: authoritative song/chart data, timing segments, notes representation, encode/decode of supported formats.
- Core data types:
  - [Vortex::Simfile](../../src/Simfile/Simfile.h)
  - [Vortex::Chart](../../src/Simfile/Chart.h#L12)
  - [Vortex::TimingData](../../src/Simfile/TimingData.h#L10)
  - [Vortex::Tempo](../../src/Simfile/Tempo.h)
  - [Vortex::NoteList](../../src/Simfile/NoteList.h)
  - [Vortex::ExpandedNote](../../src/Simfile/Notes.h#L22), [Vortex::NoteType](../../src/Simfile/Notes.h#L10), compact [Vortex::Note](../../src/Simfile/Common.h#L43)
  - Row/beat constants and helpers: [ROWS_PER_BEAT](../../src/Simfile/Tempo.h#L11), [BEATS_PER_ROW](../../src/Simfile/Tempo.h#L12), [SecPerRow()](../../src/Simfile/Tempo.h#L16), [BeatsPerMin()](../../src/Simfile/Tempo.h#L21)
- Timing trackers: [Vortex::TempoTimeTracker](../../src/Simfile/TimingData.h#L48), [Vortex::TempoRowTracker](../../src/Simfile/TimingData.h#L66)
- Segment types: [Vortex::Segment](../../src/Simfile/Segments.h#L45) and concrete segments [BpmChange](../../src/Simfile/Segments.h#L72), [Stop](../../src/Simfile/Segments.h#L83), [Delay](../../src/Simfile/Segments.h#L94), [Warp](../../src/Simfile/Segments.h#L105), [TimeSignature](../../src/Simfile/Segments.h#L116), [TickCount](../../src/Simfile/Segments.h#L127), [Combo](../../src/Simfile/Segments.h#L138), [Speed](../../src/Simfile/Segments.h#L149), [Scroll](../../src/Simfile/Segments.h#L161), [Fake](../../src/Simfile/Segments.h#L172), [Label](../../src/Simfile/Segments.h#L183)
- Formats and parsing:
  - Loaders: [LoadSm.cpp](../../src/Simfile/LoadSm.cpp), [LoadDwi.cpp](../../src/Simfile/LoadDwi.cpp), [LoadOsu.cpp](../../src/Simfile/LoadOsu.cpp)
  - Savers: [SaveSm.cpp](../../src/Simfile/SaveSm.cpp), [SaveOsu.cpp](../../src/Simfile/SaveOsu.cpp)
  - Utilities: [Parsing](../../src/Simfile/Parsing.h)
- SM5 analogs:
  - Simfile ↔ Song; Chart ↔ Steps; Style ↔ StepsType
  - TimingData, BPMS, STOPS, WARPS, TIME_SIGNATURES map directly to segment types above
  - NoteList/Notes ↔ NoteData and TapNote/TapNoteType

Managers (stateful controllers)

- Role: bridge between data model and editor UI; own session state, provide transformations and editing operations, expose clipboard and selection behaviors for their domain.
- Key controllers:
  - Tempo: [Vortex::TempoMan](../../src/Managers/TempoMan.h#L8) — conversion APIs (time/beat/row), segment editing, display BPM, tweaking workflows, timing mode (unified/song/steps)
  - Charts/Notes/Simfiles: [ChartMan](../../src/Managers/ChartMan.h), [NoteMan](../../src/Managers/NoteMan.h), [SimfileMan](../../src/Managers/SimfileMan.h)
  - Presentation/config: [StyleMan](../../src/Managers/StyleMan.h), [NoteskinMan](../../src/Managers/NoteskinMan.h), [MetadataMan](../../src/Managers/MetadataMan.h)
- SM5 analogs: managers align with portions of ScreenEdit/NoteData/TimingData manipulation, NoteSkinManager, StepsType logic.

Editor Interface (views and UI surfaces)

- Role: main editing surfaces and HUD-like panels; render the model via Core graphics and route input to Managers/Editing.
- Major views/widgets: [Vortex::Editor](../../src/Editor/Editor.h#L7), [View](../../src/Editor/View.h), [Notefield](../../src/Editor/Notefield.h), [Minimap](../../src/Editor/Minimap.h), [Waveform](../../src/Editor/Waveform.h), [TempoBoxes](../../src/Editor/TempoBoxes.h), [Menubar](../../src/Editor/Menubar.h), [Statusbar](../../src/Editor/Statusbar.h), [TextOverlay](../../src/Editor/TextOverlay.h)
- SM5 analogs: Notefield ↔ NoteField, menus/status/overlays ↔ ScreenEdit HUD components.

Editor Editing (selection, actions, history, tools)

- Role: user operations and transforms on the model, with undo/redo and selection semantics; bulk tools like stream generation and rating estimation.
- Components: [Editing](../../src/Editor/Editing.h), [Selection](../../src/Editor/Selection.h), [Action](../../src/Editor/Action.h), [History](../../src/Editor/History.h), [Shortcuts](../../src/Editor/Shortcuts.h), [StreamGenerator](../../src/Editor/StreamGenerator.h), [RatingEstimator](../../src/Editor/RatingEstimator.h), [Common](../../src/Editor/Common.h)
- SM5 analogs: similar to ScreenEdit commands and Edit Mode transforms, but editor-specific.

Editor Audio and Analysis

- Role: playback engine, synchronization with notefield/waveform, audio analysis for onset/tempo detection, format decoding, Ogg conversion.
- Components: [Music](../../src/Editor/Music.h), [Sound](../../src/Editor/Sound.h), [FFT](../../src/Editor/FFT.cpp), [Butterworth](../../src/Editor/Butterworth.h), [FindOnsets](../../src/Editor/FindOnsets.h), [FindTempo](../../src/Editor/FindTempo.h), [LoadMp3](../../src/Editor/LoadMp3.cpp), [LoadOgg](../../src/Editor/LoadOgg.cpp), [LoadWav](../../src/Editor/LoadWav.cpp), [ConvertToOgg](../../src/Editor/ConvertToOgg.h), [Aubio](../../src/Editor/Aubio.h)
- External libs: libmad (MP3), libogg/libvorbis, FreeType (fonts)

Dialogs (feature workflows)

- Role: workflow-specific UIs: synchronization, tempo tools, chart/song properties, snaps, zoom, waveform settings, dancing bot, generators.
- Examples: [AdjustSync](../../src/Dialogs/AdjustSync.h), [AdjustTempo](../../src/Dialogs/AdjustTempo.h), [AdjustTempoSM5](../../src/Dialogs/AdjustTempoSM5.h), [Zoom](../../src/Dialogs/Zoom.h), [CustomSnap](../../src/Dialogs/CustomSnap.h), [ChartProperties](../../src/Dialogs/ChartProperties.h), [SongProperties](../../src/Dialogs/SongProperties.h), [WaveformSettings](../../src/Dialogs/WaveformSettings.h), [DancingBot](../../src/Dialogs/DancingBot.h), [GenerateNotes](../../src/Dialogs/GenerateNotes.h), [ChartList](../../src/Dialogs/ChartList.h), [NewChart](../../src/Dialogs/NewChart.h), [TempoBreakdown](../../src/Dialogs/TempoBreakdown.h)

Cross-subsystem flows

- Time/beat/row conversions:
  - Produced by [Vortex::TimingData](../../src/Simfile/TimingData.h#L10) and trackers [TempoTimeTracker](../../src/Simfile/TimingData.h#L48), [TempoRowTracker](../../src/Simfile/TimingData.h#L66)
  - Exposed to UI/tools via [Vortex::TempoMan](../../src/Managers/TempoMan.h#L8)
- Editing lifecycle:
  - UI gestures in Editor Interface → map to [Editing](../../src/Editor/Editing.h) and [Action](../../src/Editor/Action.h) → model mutations in [NoteList](../../src/Simfile/NoteList.h) and tempo segments via [TempoMan](../../src/Managers/TempoMan.h#L8)
  - Undo/redo via [History](../../src/Editor/History.h)
- Playback sync:
  - [Music](../../src/Editor/Music.h) drives transport; waveforms via [Waveform](../../src/Editor/Waveform.h); timing alignment via [TimingData](../../src/Simfile/TimingData.h#L10) and [Tempo](../../src/Simfile/Tempo.h)

StepMania 5 quick mapping

- Song ↔ [Simfile](../../src/Simfile/Simfile.h)
- Steps ↔ [Chart](../../src/Simfile/Chart.h#L12)
- StepsType ↔ [Style](../../src/Managers/StyleMan.h)
- TimingData and segments ↔ [TimingData](../../src/Simfile/TimingData.h#L10), [Segments](../../src/Simfile/Segments.h#L45)
- NoteData/TapNote ↔ [NoteList](../../src/Simfile/NoteList.h), [Note](../../src/Simfile/Common.h#L43), [ExpandedNote](../../src/Simfile/Notes.h#L22)
- NoteField ↔ [Notefield](../../src/Editor/Notefield.h)

Boundaries and dependencies

- Simfile is pure data; Managers mediate all edits and provide canonical conversions.
- Editor Interface depends on Managers and Core; it should not directly mutate Simfile internals without a Manager or Editing action.
- Editor Audio consumes System+Core; it notifies UI and Managers about transport state.
- Dialogs orchestrate specific flows but rely on Managers for state changes.

Notes for StepMania maintainers

This breakdown intentionally mirrors SM5 concepts where feasible. Where naming differs, the data shapes and responsibilities are analogous even if implementation details or rendering systems differ.
