# MASTER PROMPT

Goal

Enable any contributor or AI assistant to complete tasks with minimal back-and-forth by providing high-impact context and canonical code anchors. Use StepMania 5 terminology where analogous. Cross-reference the docs for deeper details.

Project context

- ArrowVortex is a Windows C++ simfile editor (StepMania/ITG/SSC/DWI/osu).
- Build with Visual Studio 2022: open [build/VisualStudio/ArrowVortex.sln](../build/VisualStudio/ArrowVortex.sln).
- Core grid uses 48 rows/beat; see [src/Simfile/Tempo.h](../src/Simfile/Tempo.h). Constants: [ROWS_PER_BEAT](../src/Simfile/Tempo.h#L11), [BEATS_PER_ROW](../src/Simfile/Tempo.h#L12).

Quick links (read these first)

- Architecture overview and diagram: [docs/architecture/overview.md](./architecture/overview.md)
- Subsystems deep dive: [docs/architecture/subsystems.md](./architecture/subsystems.md)
- StepMania 5 mapping: [docs/architecture/stepmania-mapping.md](./architecture/stepmania-mapping.md)
- Glossary (timing, segments, notes): [docs/glossary.md](./glossary.md)
- Small focused task backlog: [docs/roadmap.md](./roadmap.md)
- Docs index: [docs/README.md](./README.md)

Primary subsystems (1-line orientation)

- Core: utilities, rendering, text, GUI toolkit. Start at [src/Core/](../src/Core/).
- System: OS/files/threads/audio/OpenGL. Start at [src/System/](../src/System/).
- Simfile (model and formats): data model of song/charts/notes/timing + loaders/savers. Start at [src/Simfile/](../src/Simfile/).
- Managers: stateful controllers that mediate edits and expose conversions. Start at [src/Managers/](../src/Managers/).
- Editor Interface: views and HUD surfaces (notefield/minimap/waveform/tempo boxes). Start at [src/Editor/](../src/Editor/).
- Editor Editing: selection/actions/history/shortcuts/tools.
- Editor Audio and Analysis: playback, waveform, onset/tempo detection.
- Dialogs: feature workflows (sync, tempo, snaps, properties, etc.). Start at [src/Dialogs/](../src/Dialogs/).

Canonical code anchors (StepMania-aligned)

- Timing and conversion:
  - [Vortex::TimingData](../src/Simfile/TimingData.h#L10)
  - [Vortex::TempoTimeTracker](../src/Simfile/TimingData.h#L48)
  - [Vortex::TempoRowTracker](../src/Simfile/TimingData.h#L66)
- Data model:
  - [Vortex::Chart](../src/Simfile/Chart.h#L12)
  - [Vortex::Tempo](../src/Simfile/Tempo.h#L39)
  - [Vortex::NoteList](../src/Simfile/NoteList.h#L10)
  - [Vortex::ExpandedNote](../src/Simfile/Notes.h#L22)
  - Segment base and types: [Vortex::Segment](../src/Simfile/Segments.h#L45)
- Controllers (Managers):
  - [Vortex::TempoMan](../src/Managers/TempoMan.h#L8)
- Editor/application surface:
  - [Vortex::Editor](../src/Editor/Editor.h#L7)

Where things live (common tasks)

- Load/save simfiles and formats: loaders/savers under [src/Simfile/](../src/Simfile/). See file anchors in [docs/architecture/subsystems.md](./architecture/subsystems.md).
- Timing segments (BPMS/STOPS/DELAYS/WARPS/etc.): types in [src/Simfile/Segments.h](../src/Simfile/Segments.h), container in [src/Simfile/Tempo.h](../src/Simfile/Tempo.h), conversions via [Vortex::TimingData](../src/Simfile/TimingData.h#L10) and [Vortex::TempoMan](../src/Managers/TempoMan.h#L8).
- Notes and editing: [Vortex::NoteList](../src/Simfile/NoteList.h#L10), edit prep at [prepareEdit](../src/Simfile/NoteList.h#L35); selection/actions/history under [src/Editor/](../src/Editor/).
- Notefield/UI surfaces: Notefield/Minimap/Waveform/TempoBoxes under [src/Editor/](../src/Editor/).
- Audio playback and analysis: Music/Sound/FFT/FindOnsets/FindTempo under [src/Editor/](../src/Editor/).
- Styles and columns: [Style/StyleMan](../src/Managers/StyleMan.h), noteskins via [NoteskinMan](../src/Managers/NoteskinMan.h).

How to execute any task (minimal checklist)

1) Identify scope and subsystem
- Use the Subsystems doc: [docs/architecture/subsystems.md](./architecture/subsystems.md) to locate the owning area.
- For SM-aligned tasks, consult the mapping: [docs/architecture/stepmania-mapping.md](./architecture/stepmania-mapping.md).

2) Find entry points and types
- Use the anchors above and follow includes from the owning header into implementation.
- For timing/notes work, rely on [Vortex::TimingData](../src/Simfile/TimingData.h#L10), [Vortex::TempoMan](../src/Managers/TempoMan.h#L8), [Vortex::NoteList](../src/Simfile/NoteList.h#L10).

3) Make focused changes
- Prefer editing via Managers and Editing actions rather than mutating raw model structures directly.
- Keep 48-rows-per-beat semantics intact; use helpers [SecPerRow](../src/Simfile/Tempo.h#L16) and [BeatsPerMin](../src/Simfile/Tempo.h#L21) for conversions.

4) Validate and surface behavior
- For UI: verify in Editor surfaces (Notefield/TempoBoxes/Waveform).
- For timing/format changes: test load/save and time/row/beat invertibility. See test ideas in [docs/roadmap.md](./roadmap.md).

5) Document and link
- If behavior changes or new capability is added, add or adjust a short note in the relevant doc and keep code anchors clickable.

Build and run

- Open and build [ArrowVortex.sln](../build/VisualStudio/ArrowVortex.sln) in Visual Studio 2022 (Desktop development with C++).
- External libs included via solution projects: FreeType, libmad, libogg/libvorbis (see solution for references).

StepMania framing (at a glance)

- Simfile ↔ Song; Chart ↔ Steps; Style ↔ StepsType.
- NoteList/Notes/ExpandedNote ↔ NoteData/TapNote + editor metadata.
- TimingData/segments ↔ BPMS/STOPS/DELAYS/WARPS/TIME_SIGNATURES/etc.
- Notefield ↔ NoteField.
- Full mapping: [docs/architecture/stepmania-mapping.md](./architecture/stepmania-mapping.md).

When more context is needed

- Start with the overview and diagram: [docs/architecture/overview.md](./architecture/overview.md)
- Dive into a specific area: [docs/architecture/subsystems.md](./architecture/subsystems.md)
- Clarify terms: [docs/glossary.md](./glossary.md)
- Pick a ready-to-run improvement: [docs/roadmap.md](./roadmap.md)

Conventions to keep interactions efficient

- Reference concrete files and constructs in messages using clickable anchors, e.g. [Vortex::TempoRowTracker](../src/Simfile/TimingData.h#L66), [src/Simfile/Segments.h](../src/Simfile/Segments.h).
- State the subsystem you are targeting and the primary type(s) being changed up front.
- For timing or note edits, include before/after rows/beats/time samples for one short case.
