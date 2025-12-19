# 🎹 Tuning Explorer

An interactive web-based tool for exploring and comparing historical tuning systems through sound. Experience how different temperaments affect musical intervals, melodies, and harmony.

**[🎵 Try It Live](https://ajosephsen.github.io/TuningExplorer/)** - No installation required, works in any modern browser!

## Features

### 🎵 Multiple Tuning Systems
- **12-TET (Equal Temperament)** - Modern standard tuning
- **Just Intonation** - Pure integer ratios for consonant intervals
- **Pythagorean** - Built from perfect fifths, sharp major thirds
- **Quarter-Comma Meantone** - Renaissance tuning with pure major thirds and wolf fifths

### 🎹 Interactive Keyboard
- 12-key chromatic keyboard (C to B)
- QWERTY keyboard mapping (A, W, S, E, D, F, T, G, Y, H, U, J)
- Mouse/touch support
- Real-time visual feedback

### 🎺 Multiple Instruments
- Pure Sine (flute-like)
- Triangle, Square, Sawtooth waves
- Organ (rich harmonics)
- Plucked String
- Bell (inharmonic partials)

### 📊 Real-Time Visualization
- **Oscilloscope** - Time-domain waveform display
- **FFT Spectrum** - Frequency spectrum analyzer
- **Interval Analysis** - When playing two notes:
  - Identifies closest simple ratio (e.g., "Perfect 5th ~1.5000:1")
  - Shows cents deviation from 12-TET
  - Color-coded accuracy (green/orange/red)

### 🎼 Melody Mode
- Toggle to play "Twinkle Twinkle Little Star"
- Melody transposes to any starting key
- Includes bass line one octave down
- Extends properly across octaves in all tuning systems
- Hear how different temperaments affect familiar melodies

### ⚠️ Problematic Intervals
Demonstrates characteristic weaknesses in each tuning system:
- **Wolf fifths** in Quarter-Comma Meantone (G# → Eb)
- **Sharp major thirds** in 12-TET and Pythagorean
- **Wolf intervals** that sound dissonant due to accumulated tuning error

## Try It Live

Open `index.html` in any modern web browser - no build step required!

Or serve locally:
```bash
python3 -m http.server
```

## How It Works

Each tuning system defines 12 frequency ratios relative to C4 (261.63 Hz). When you play notes:

1. **Single notes** play with selected instrument and ADSR envelope
2. **Two notes** trigger interval analysis showing ratio and tuning deviation
3. **Melody mode** sequences notes automatically, demonstrating how tunings affect melodies

The visualizers use Web Audio API's `AnalyserNode` to display real-time waveforms and frequency content.

## Music Theory Background

**Why different tunings?**
- Pure intervals (like 3:2 fifths and 5:4 major thirds) sound more consonant
- Impossible to have ALL intervals pure in 12-note system
- Different tunings make different compromises:
  - **12-TET**: All keys equal, but thirds are sharp
  - **Just Intonation**: Pure thirds and fifths in one key, problems elsewhere
  - **Pythagorean**: Pure fifths, but very sharp major thirds
  - **Meantone**: Pure major thirds, but creates "wolf" fifths in remote keys

## Technical Details

- Pure vanilla JavaScript - no frameworks or dependencies
- Single-file architecture (~1000 lines)
- Web Audio API for synthesis
- Canvas 2D for visualization
- Works offline after initial load

## License

MIT