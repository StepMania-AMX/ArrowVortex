# ArrowVortex Documentation

Purpose and audience

This documentation provides a 10000 ft view of the ArrowVortex editor, organized into subsystems and explained using StepMania 5 terminology where analogous. It is intended to help maintainers and contributors familiar with StepMania quickly orient themselves in this codebase.

Important note for StepMania maintainers

These docs intentionally reference StepMania 5 concepts (e.g., Song, Steps, TimingData, NoteData, NoteField). Where ArrowVortex uses different names, an explicit mapping is provided. See [architecture/stepmania-mapping.md](./architecture/stepmania-mapping.md).

Repository anchors and entry points

- Visual Studio solution and logical groups:
  - [ArrowVortex.sln](../build/VisualStudio/ArrowVortex.sln)
  - [ArrowVortex.vcxproj.filters](../build/VisualStudio/ArrowVortex.vcxproj.filters)
- Root project readme:
  - [README.md](../README.md)

How this documentation is organized

- Architecture overview
  - [architecture/overview.md](./architecture/overview.md)
  - High-level components and data flow, with a Mermaid diagram.
- Subsystems deep dive
  - [architecture/subsystems.md](./architecture/subsystems.md)
  - Responsibilities, key files and types, and interactions for each subsystem.
- StepMania mapping
  - [architecture/stepmania-mapping.md](./architecture/stepmania-mapping.md)
  - A focused ArrowVortex ↔ StepMania 5 concept map.
- Glossary
  - [glossary.md](./glossary.md)
  - Terminology used across the codebase and editor UI.
- Roadmap
  - [roadmap.md](./roadmap.md)
  - A curated list of small, focused tasks aligned with subsystems.

Quick orientation by subsystem

- Core: fundamental types and utilities, GUI toolkit, and rendering
  - Examples: [String](../src/Core/String.h), [Vector](../src/Core/Vector.h), [ByteStream](../src/Core/ByteStream.h), [GuiContext](../src/Core/GuiContext.h), [Renderer](../src/Core/Renderer.h)
- System: OS integration, file I/O, threading, OpenGL setup, audio mixer
  - Examples: [System](../src/System/System.h), [File](../src/System/File.h), [Thread](../src/System/Thread.h), [Mixer](../src/System/Mixer.h), [OpenGL](../src/System/OpenGL.h)
- Simfile model and formats: song and charts, timing, notes, parsing and saving
  - Examples: [Simfile](../src/Simfile/Simfile.h), [Chart](../src/Simfile/Chart.h#L12), [TimingData](../src/Simfile/TimingData.h#L10), [Tempo](../src/Simfile/Tempo.h), [NoteList](../src/Simfile/NoteList.h), [Notes](../src/Simfile/Notes.h)
- Managers: stateful controllers for active simfile, chart, notes, tempo, styles, noteskins, metadata
  - Examples: [TempoMan](../src/Managers/TempoMan.h#L8), [ChartMan](../src/Managers/ChartMan.h), [NoteMan](../src/Managers/NoteMan.h), [SimfileMan](../src/Managers/SimfileMan.h), [StyleMan](../src/Managers/StyleMan.h), [NoteskinMan](../src/Managers/NoteskinMan.h), [MetadataMan](../src/Managers/MetadataMan.h)
- Editor interface: main view composition and UI surfaces (notefield, minimap, waveform, tempo boxes, menus, status bar)
  - Examples: [Editor](../src/Editor/Editor.h#L7), [View](../src/Editor/View.h), [Notefield](../src/Editor/Notefield.h), [Minimap](../src/Editor/Minimap.h), [Waveform](../src/Editor/Waveform.h), [TempoBoxes](../src/Editor/TempoBoxes.h)
- Editor editing and tools: selection, actions, history, shortcuts, generators and estimators
  - Examples: [Editing](../src/Editor/Editing.h), [Selection](../src/Editor/Selection.h), [Action](../src/Editor/Action.h), [History](../src/Editor/History.h), [Shortcuts](../src/Editor/Shortcuts.h), [StreamGenerator](../src/Editor/StreamGenerator.h), [RatingEstimator](../src/Editor/RatingEstimator.h)
- Editor audio and analysis: playback, synchronization, waveform, onset and tempo detection, format loading, Ogg conversion
  - Examples: [Music](../src/Editor/Music.h), [Sound](../src/Editor/Sound.h), [FFT](../src/Editor/FFT.cpp), [FindOnsets](../src/Editor/FindOnsets.h), [FindTempo](../src/Editor/FindTempo.h), [LoadMp3](../src/Editor/LoadMp3.cpp), [LoadOgg](../src/Editor/LoadOgg.cpp), [LoadWav](../src/Editor/LoadWav.cpp), [ConvertToOgg](../src/Editor/ConvertToOgg.h)
- Dialogs: feature-specific workflows (adjust sync, tempo, zoom, custom snaps, chart/song properties, waveform settings, dancing bot, generate notes)
  - Examples: [AdjustSync](../src/Dialogs/AdjustSync.h), [AdjustTempo](../src/Dialogs/AdjustTempo.h), [AdjustTempoSM5](../src/Dialogs/AdjustTempoSM5.h), [Zoom](../src/Dialogs/Zoom.h), [CustomSnap](../src/Dialogs/CustomSnap.h)

StepMania framing guide

- Simfile ↔ Song: ArrowVortex [Simfile](../src/Simfile/Simfile.h) corresponds to StepMania Song.
- Chart ↔ Steps: ArrowVortex [Chart](../src/Simfile/Chart.h#L12) corresponds to SM Steps.
- Style ↔ StepsType: style and game type selection align with SM StepsType.
- Notes and NoteList ↔ NoteData and TapNote: ArrowVortex [NoteList](../src/Simfile/NoteList.h) and [Notes](../src/Simfile/Notes.h) align with SM NoteData and TapNote.
- TimingData and segments (BPMS, STOPS, WARPS, TIME_SIGNATURES) ↔ SM TimingData: ArrowVortex [TimingData](../src/Simfile/TimingData.h#L10) and tempo segments align with SM timing constructs.
- Notefield ↔ NoteField: ArrowVortex [Notefield](../src/Editor/Notefield.h) corresponds to SM NoteField rendering and interaction.

Contributing to docs

- Open issues or PRs for clarifications and additions.
- Keep code references clickable and up to date.
- Prefer small, incremental improvements aligned with the subsystem structure.

Changelog for docs

- Initial structure created with architecture overview, and placeholders for subsystem details, StepMania mapping, glossary, and roadmap.
