# Copilot Instructions

## Project Snapshot
- Single-page **Tuning Explorer** with playable keyboard, real-time visualizers (oscilloscope + FFT spectrum), and multiple historical tuning systems (12-TET, Just Intonation, Pythagorean, Quarter-Comma Meantone).
- Entire app lives in [index.html](index.html) - ~650 lines including inline `<style>` and `<script>` blocks. No build step, no external dependencies.
- Core state: `activeOscillators` Map tracks playing notes by index; `currentFrequencies` Map powers the frequency display; `audioContext`, `analyser`, and `animationId` manage Web Audio and visualizer lifecycle.
- Keyboard layout: 12 chromatic keys (C-B) mapped to QWERTY keys (A, W, S, E, D, F, T, G, Y, H, U, J) with mouse/touch support via `.key` elements.

## Architecture & Data Flow
**Tuning Systems** (`tunings` object):
- Each system defines 12 ratios relative to C4 (261.63 Hz). `getFrequency(noteIndex)` multiplies base by current tuning's ratio.
- UI updates: `updateFrequencyTable()` regenerates HTML `<table>` showing note/ratio/freq/cents on tuning dropdown change.

**Sound Generation** (`createInstrumentOscillator(freq)`):
- Returns `{ oscillators, gains, inst }` - supports single oscillators (sine/square/sawtooth/triangle), multi-oscillator "organ" (4 harmonic partials), and "bell" (4 inharmonic partials at 1×, 2.4×, 3.8×, 5.2× freq).
- ADSR envelope applied via `gain.gain` ramps: attack → sustain (immediate), release on note-off via `stopNote()`.
- All gains connect to shared `analyser` node for visualization; `activeOscillators` Map prevents duplicate notes and manages cleanup.

**Visualizers** (`startVisualization()`):
- `requestAnimationFrame` loop draws two canvases: time-domain waveform (green oscilloscope) and frequency bars (hsl gradient spectrum).
- `analyser.getByteTimeDomainData(dataArray)` + `getByteFrequencyData(freqArray)` feed both canvases; loop starts once on first audio context init.

**Interval Playback** (`playInterval(note1, note2)`):
- Creates two simultaneous oscillators, auto-stops after 2s, temporarily updates `currentFrequencies` and adds `.active` class to keyboard keys.
- "Problematic Intervals" section demonstrates wolf fifths and tuning weaknesses via hardcoded note-pair buttons.

## Local Workflow
- **No build step**: edit [index.html](index.html) and reload. Serve with `python3 -m http.server` or open file directly in browser.
- [package.json](package.json) is metadata-only - no `npm install`, no scripts.
- Keep everything inline: new UI/logic belongs in existing `<style>`/`<script>` blocks unless restructuring to multi-file.

## Coding Patterns
- **Vanilla DOM**: direct element creation (`document.createElement`, `addEventListener`), inline event handlers (`onclick`, `onchange`), no frameworks.
- **Global state management**: functions like `playNote`, `stopNote`, `playInterval` read/write shared Maps and the `audioContext`. Adding features should follow this pattern.
- **CSS-in-HTML**: grid layouts (`.interval-buttons`), flex containers (`.main-wrapper`), inline styles for one-offs. Purple gradient background via `body` linear-gradient.
- **Note indexing**: 0-11 for C-B; `notes` array holds labels, `keys` array holds QWERTY mappings. Add new features by iterating these arrays.
- **Envelope scheduling**: always `cancelScheduledValues(now)` before new ramps to avoid accumulation. Use `exponentialRampToValueAtTime(0.01, ...)` for smooth release (can't ramp to 0).

## Web Audio Patterns
- **Lifecycle**: `initAudioContext()` lazy-creates singleton `audioContext` + `analyser` on first interaction. Never create multiple contexts.
- **Oscillator cleanup**: Map each note to `{ oscillators[], gains[], inst }`, call `.stop(time)` and delete Map entry in `stopNote()` to prevent zombie nodes.
- **Keyboard event handling**: `keyPressed` Set prevents retriggering on key-hold; `keydown` adds notes, `keyup` removes. Mouse events (`mousedown`/`mouseup`/`mouseleave`) mirror this for touch.
- **Visual feedback**: `.active` class on `.key` elements, synced with audio start/stop, removed via `setTimeout` for interval playback.

## External APIs
- `AudioContext || webkitAudioContext` for Safari compatibility.
- `OscillatorNode`, `GainNode`, `AnalyserNode` (fftSize 2048), `Canvas 2D` - all standard APIs with broad support.
- `requestAnimationFrame` for 60fps visualizer loop.

## Agent Tips
- **Adding instruments**: extend `instruments` object with ADSR params, handle new `wave` types in `createInstrumentOscillator()`.
- **New tuning systems**: add entry to `tunings` with 12-element `ratios` array, update `<option>` in HTML, call `updateFrequencyTable()`.
- **Debugging audio**: log `audioContext.state`, `activeOscillators.size`, or oscillator frequencies to console; remove logs before committing.
- **UI expansion**: append to existing sections rather than creating new containers—keeps DOM minimal. Use inline `style` attributes sparingly.
- **Performance**: `Map.delete()` actively cleans up oscillators; avoid letting `activeOscillators` grow unbounded. Stop visualization loop via `cancelAnimationFrame(animationId)` if needed (not currently implemented).
