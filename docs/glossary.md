# Glossary

Purpose

Concise definitions of ArrowVortex and StepMania terms used across the documentation and codebase. Where possible, terms link to concrete code anchors.

Core timing and grid

- Rows per beat
  - Fixed grid of 48 rows per beat used for quantization and timing.
  - Constants: [ROWS_PER_BEAT](../src/Simfile/Tempo.h#L11), [BEATS_PER_ROW](../src/Simfile/Tempo.h#L12).
- BPM
  - Beats per minute, defines tempo; represented as seconds per row internally via [SecPerRow()](../src/Simfile/Tempo.h#L16) and [BeatsPerMin()](../src/Simfile/Tempo.h#L21).
  - Editable via BPM segments [Vortex::BpmChange](../src/Simfile/Segments.h#L72).
- Beat, measure
  - Beat index is derived from time/row using [Vortex::TimingData::timeToBeat()](../src/Simfile/TimingData.h#L30) and [beatToMeasure()](../src/Simfile/TimingData.h#L39).
- Offset
  - Global time offset of music from beat 0; held in [Vortex::Tempo::offset](../src/Simfile/Tempo.h#L53) and editable through [Vortex::TempoMan::setOffset()](../src/Managers/TempoMan.h#L69).

Timing segments (StepMania-aligned)

- BPMS
  - Tempo changes; [Vortex::BpmChange](../src/Simfile/Segments.h#L72).
- STOPS
  - Pauses in playback; [Vortex::Stop](../src/Simfile/Segments.h#L83) (seconds).
- DELAYS
  - SSC delays; [Vortex::Delay](../src/Simfile/Segments.h#L94) (seconds).
- WARPS
  - Skip rows without consuming time; [Vortex::Warp](../src/Simfile/Segments.h#L105) (numRows).
- TIME_SIGNATURES
  - Meter changes; [Vortex::TimeSignature](../src/Simfile/Segments.h#L116) (rowsPerMeasure, beatNote).
- TICK_COUNTS
  - Per-measure tick density; [Vortex::TickCount](../src/Simfile/Segments.h#L127).
- COMBOS
  - Hit/miss combo scaling; [Vortex::Combo](../src/Simfile/Segments.h#L138).
- SPEEDS
  - Visual scroll speed independent of time; [Vortex::Speed](../src/Simfile/Segments.h#L149) (ratio, delay, unit).
- SCROLLS
  - Legacy visual scroll ratio; [Vortex::Scroll](../src/Simfile/Segments.h#L161).
- FAKES
  - Non-scoring regions; [Vortex::Fake](../src/Simfile/Segments.h#L172).
- LABELS
  - Named markers; [Vortex::Label](../src/Simfile/Segments.h#L183).

Data model

- Simfile
  - Entire song data; [Vortex::Simfile](../src/Simfile/Simfile.h).
- Chart
  - Steps for a given style/difficulty; [Vortex::Chart](../src/Simfile/Chart.h#L12).
- Style
  - Game type and pad geometry; [Vortex::Style](../src/Managers/StyleMan.h#L8).
- Notes
  - Compact representation: [Vortex::Note](../src/Simfile/Common.h#L43).
  - Expanded editor note with metadata: [Vortex::ExpandedNote](../src/Simfile/Notes.h#L22).
  - Note collection: [Vortex::NoteList](../src/Simfile/NoteList.h).
- Note types
  - [Vortex::NoteType](../src/Simfile/Notes.h#L10): STEP_OR_HOLD, MINE, ROLL, LIFT, FAKE.
- TimingData
  - Precomputed time/row/beat mappings; [Vortex::TimingData](../src/Simfile/TimingData.h#L10).
  - Trackers: [Vortex::TempoTimeTracker](../src/Simfile/TimingData.h#L48), [Vortex::TempoRowTracker](../src/Simfile/TimingData.h#L66).
- Tempo
  - Container for segments, offset, display BPM; [Vortex::Tempo](../src/Simfile/Tempo.h#L39).

Editor and UI

- Notefield
  - Visual representation of columns and notes; [Vortex::Notefield](../src/Editor/Notefield.h).
- Minimap
  - Scrollable overview of chart; [Vortex::Minimap](../src/Editor/Minimap.h).
- Waveform
  - Audio waveform display; [Vortex::Waveform](../src/Editor/Waveform.h).
- Tempo boxes
  - UI for segment timelines; [Vortex::TempoBoxes](../src/Editor/TempoBoxes.h).
- Editor
  - App controller and lifecycle; [Vortex::Editor](../src/Editor/Editor.h#L7).

Managers

- TempoMan
  - Timing conversion and segment editing; [Vortex::TempoMan](../src/Managers/TempoMan.h#L8). Modes: TimingMode, TweakMode, SelectionRange.
- ChartMan, NoteMan, SimfileMan, StyleMan, NoteskinMan, MetadataMan
  - Controllers bridging UI and data; see headers under src/Managers/.

Editing concepts

- Selection
  - Editable region and note selection; [Vortex::Selection](../src/Editor/Selection.h).
- Action and History
  - Undoable operations; [Vortex::Action](../src/Editor/Action.h), [Vortex::History](../src/Editor/History.h).
- PrepareEdit
  - Conflict-resolution step for note edits; [Vortex::NoteList::prepareEdit()](../src/Simfile/NoteList.h#L35).

Audio and analysis

- Music/Sound
  - Transport and playback; [Vortex::Music](../src/Editor/Music.h), [Vortex::Sound](../src/Editor/Sound.h).
- Onset/tempo detection
  - [FindOnsets](../src/Editor/FindOnsets.h), [FindTempo](../src/Editor/FindTempo.h).
- FFT/filters
  - [FFT](../src/Editor/FFT.cpp), [Butterworth](../src/Editor/Butterworth.h).
- Format loaders
  - [LoadMp3](../src/Editor/LoadMp3.cpp), [LoadOgg](../src/Editor/LoadOgg.cpp), [LoadWav](../src/Editor/LoadWav.cpp), [ConvertToOgg](../src/Editor/ConvertToOgg.h).

Editor tools and features

- Visual sync
  - Sub-beat placement and alignment using TimingData trackers and TempoMan tweak modes.
- Custom snaps
  - Nonstandard quantization stored per note via ExpandedNote.quant; [Vortex::ExpandedNote](../src/Simfile/Notes.h#L57) and editor UI.
- Dancing Bot
  - Playback visualization tool; [Dialogs::DancingBot](../src/Dialogs/DancingBot.h).
- Stream generator, rating estimator
  - [StreamGenerator](../src/Editor/StreamGenerator.h), [RatingEstimator](../src/Editor/RatingEstimator.h).

Community terms

- Noteskin
  - Visual assets for notes; managed by [NoteskinMan](../src/Managers/NoteskinMan.h).
- Radar values
  - Chart metrics (density, stream, etc.); stored in [Vortex::Chart::radar](../src/Simfile/Chart.h#L38).
- Meter and difficulty
  - [Vortex::Difficulty](../src/Simfile/Common.h#L24) and [Vortex::Chart::meter](../src/Simfile/Chart.h#L40).

File formats

- SM/SSC, DWI, osu
  - Loaders: [LoadSm.cpp](../src/Simfile/LoadSm.cpp), [LoadDwi.cpp](../src/Simfile/LoadDwi.cpp), [LoadOsu.cpp](../src/Simfile/LoadOsu.cpp)
  - Savers: [SaveSm.cpp](../src/Simfile/SaveSm.cpp), [SaveOsu.cpp](../src/Simfile/SaveOsu.cpp)
  - Parsing helpers: [Parsing](../src/Simfile/Parsing.h)
