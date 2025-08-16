# StepMania 5 Mapping

Purpose

This document maps ArrowVortex concepts and types to StepMania 5 terms to help SM maintainers navigate this codebase quickly. It references concrete ArrowVortex source locations for each concept.

Core data model

- Song ↔ [Vortex::Simfile](../../src/Simfile/Simfile.h)
- Steps ↔ [Vortex::Chart](../../src/Simfile/Chart.h#L12)
- StepsType (style) ↔ [Vortex::Style](../../src/Managers/StyleMan.h#L8)
- TimingData ↔ [Vortex::TimingData](../../src/Simfile/TimingData.h#L10)
- NoteData/TapNote ↔ [Vortex::NoteList](../../src/Simfile/NoteList.h), [Vortex::Note](../../src/Simfile/Common.h#L43), [Vortex::ExpandedNote](../../src/Simfile/Notes.h#L22)
- Display BPM ↔ [DisplayBpm](../../src/Simfile/Tempo.h#L27), [BpmRange](../../src/Simfile/Tempo.h#L35)
- Transport/time conversion helpers ↔ [ROWS_PER_BEAT](../../src/Simfile/Tempo.h#L11), [BEATS_PER_ROW](../../src/Simfile/Tempo.h#L12), [SecPerRow()](../../src/Simfile/Tempo.h#L16), [BeatsPerMin()](../../src/Simfile/Tempo.h#L21)
- Per-song/per-chart tempo container ↔ [Vortex::Tempo](../../src/Simfile/Tempo.h#L39)

Timing and segments

StepMania timing constructs map directly to ArrowVortex segment types. All segment structs derive from [Vortex::Segment](../../src/Simfile/Segments.h#L45).

- #BPMS ↔ [Vortex::BpmChange](../../src/Simfile/Segments.h#L72) with field bpm
- #STOPS ↔ [Vortex::Stop](../../src/Simfile/Segments.h#L83) with field seconds
- #DELAYS (SSC) ↔ [Vortex::Delay](../../src/Simfile/Segments.h#L94) with field seconds
- #WARPS ↔ [Vortex::Warp](../../src/Simfile/Segments.h#L105) with field numRows
- #TIME_SIGNATURES (SSC) ↔ [Vortex::TimeSignature](../../src/Simfile/Segments.h#L116) with fields rowsPerMeasure and beatNote
- #TICKCOUNTS (SSC) ↔ [Vortex::TickCount](../../src/Simfile/Segments.h#L127)
- #COMBOS (SSC) ↔ [Vortex::Combo](../../src/Simfile/Segments.h#L138)
- #SPEEDS (SSC) ↔ [Vortex::Speed](../../src/Simfile/Segments.h#L149) with fields ratio, delay, unit
- #SCROLLS (SSC) ↔ [Vortex::Scroll](../../src/Simfile/Segments.h#L161)
- #FAKES (SSC) ↔ [Vortex::Fake](../../src/Simfile/Segments.h#L172)
- #LABELS (SSC) ↔ [Vortex::Label](../../src/Simfile/Segments.h#L183)

Timing trackers

- SM equivalent behavior for converting between time/beat/row is provided by:
  - [Vortex::TempoTimeTracker](../../src/Simfile/TimingData.h#L48)
  - [Vortex::TempoRowTracker](../../src/Simfile/TimingData.h#L66)
- High-level conversion APIs exposed to the editor via [Vortex::TempoMan](../../src/Managers/TempoMan.h#L8), including timeToRow(), timeToBeat(), rowToTime(), beatToTime(), beatToMeasure().

Notes representation

- Compact storage unit ↔ [Vortex::Note](../../src/Simfile/Common.h#L43)
- Editing/expanded note (TapNote + editor metadata) ↔ [Vortex::ExpandedNote](../../src/Simfile/Notes.h#L22)
- Note types (TapNoteType analog) ↔ [Vortex::NoteType](../../src/Simfile/Notes.h#L10)
  - NOTE_STEP_OR_HOLD, NOTE_MINE, NOTE_ROLL, NOTE_LIFT, NOTE_FAKE
- Note collection (NoteData analog) ↔ [Vortex::NoteList](../../src/Simfile/NoteList.h)
  - Editing prep and conflict resolution: NoteList.prepareEdit()
  - Encode/decode with rows or timestamps: NoteList.encode(), NoteList.decode()

Styles and columns

- StepsType/game style ↔ [Vortex::Style](../../src/Managers/StyleMan.h#L8) managed by [Vortex::StyleMan](../../src/Managers/StyleMan.h#L29)
  - Columns/players: Style.numCols, Style.numPlayers
  - Mirror tables and pad geometry: Style.mirrorTableH, Style.mirrorTableV, Style.padColPositions
- Global limits: [SimConstants](../../src/Simfile/Common.h#L16) including SIM_MAX_COLUMNS and SIM_MAX_PLAYERS

Editor and UI surfaces

- Editor core (ScreenEdit analog) ↔ [Vortex::Editor](../../src/Editor/Editor.h#L7)
- NoteField rendering ↔ [Vortex::Notefield](../../src/Editor/Notefield.h)
- Waveform/minimap/tempo UI ↔ [Vortex::Waveform](../../src/Editor/Waveform.h), [Vortex::Minimap](../../src/Editor/Minimap.h), [Vortex::TempoBoxes](../../src/Editor/TempoBoxes.h)

Managers versus SM responsibilities

- ArrowVortex uses dedicated managers that correspond to portions of SM’s editing and data controllers:
  - Timing and tempo editing ↔ [Vortex::TempoMan](../../src/Managers/TempoMan.h#L8)
    - Split timing handling (unified/song/steps) ↔ TempoMan::TimingMode
    - Segment edit and clipboard flows ↔ TempoMan::modify(), copy/paste, remove/insert
    - Tweak workflows (offset/BPM/stop) ↔ TempoMan::TweakMode, start/stopTweaking()
  - Notes and charts management ↔ [ChartMan](../../src/Managers/ChartMan.h), [NoteMan](../../src/Managers/NoteMan.h), [SimfileMan](../../src/Managers/SimfileMan.h)
  - Presentation and skinning ↔ [StyleMan](../../src/Managers/StyleMan.h), [NoteskinMan](../../src/Managers/NoteskinMan.h)

Formats and I/O

- Supported formats (see project README): SM (.sm), SSC (.ssc), DWI (.dwi), osu (.osu)
- ArrowVortex loaders/savers:
  - SM/DWI/OSU load ↔ [LoadSm.cpp](../../src/Simfile/LoadSm.cpp), [LoadDwi.cpp](../../src/Simfile/LoadDwi.cpp), [LoadOsu.cpp](../../src/Simfile/LoadOsu.cpp)
  - SM/OSU save ↔ [SaveSm.cpp](../../src/Simfile/SaveSm.cpp), [SaveOsu.cpp](../../src/Simfile/SaveOsu.cpp)
  - Parsing helpers ↔ [Parsing](../../src/Simfile/Parsing.h)

Transport, audio, and analysis

- Playback transport and sync for editor ↔ [Vortex::Music](../../src/Editor/Music.h), [Vortex::Sound](../../src/Editor/Sound.h)
- Analysis utilities (onset/tempo detection) ↔ [FindOnsets](../../src/Editor/FindOnsets.h), [FindTempo](../../src/Editor/FindTempo.h), [FFT](../../src/Editor/FFT.cpp), [Butterworth](../../src/Editor/Butterworth.h)
- External libs similar in role to SM backends: libmad, libogg/libvorbis; font rendering via FreeType

Rows, beats, measures

- Both ArrowVortex and SM use 48 rows per beat as a base grid:
  - [ROWS_PER_BEAT](../../src/Simfile/Tempo.h#L11), [BEATS_PER_ROW](../../src/Simfile/Tempo.h#L12)
- Beat/measure conversions via [Vortex::TimingData](../../src/Simfile/TimingData.h#L10) and TempoMan beatToMeasure()

Notable differences and extensions

- ExpandedNote stores additional editor flags (isWarped, selection state, quantization) not present in raw SM TapNote.
- Managers provide explicit tweak modes for interactive offset/BPM/stop adjustment beyond typical SM command bindings.
- Visual sync feature and custom snaps are first-class editor tools; these are UI/editor-oriented and not persisted in core SM formats.

Where to start in the code

- Solution and logical groupings: [ArrowVortex.sln](../../build/VisualStudio/ArrowVortex.sln), [ArrowVortex.vcxproj.filters](../../build/VisualStudio/ArrowVortex.vcxproj.filters)
- Data model anchors: [Vortex::Simfile](../../src/Simfile/Simfile.h), [Vortex::Chart](../../src/Simfile/Chart.h#L12), [Vortex::Tempo](../../src/Simfile/Tempo.h#L39), [Vortex::TimingData](../../src/Simfile/TimingData.h#L10)
- UI and editing anchors: [Vortex::Editor](../../src/Editor/Editor.h#L7), [Vortex::Notefield](../../src/Editor/Notefield.h), [Vortex::Editing](../../src/Editor/Editing.h), [Vortex::History](../../src/Editor/History.h)

Note for StepMania maintainers

This mapping uses SM5 names deliberately to reduce translation cost. While rendering and UI frameworks differ (Core GUI/Renderer versus SM Actors/Theme), the data shapes, timing segments, and note abstractions are intended to feel familiar.
