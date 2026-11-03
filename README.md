# Chord Synthesizer

Chord Synthesizer is an ESP32 instrument that plays complete chords from physical buttons. The idea is to let someone explore extended harmonies without playing every note separately on a keyboard. The controls change the chord, octave, sound and volume.

For example, C major seventh contains C, E, G and B. Adding D makes C major ninth. The instrument plays those notes together, with one chord active at a time.

## How it works

### Controls

Chained input shift registers collect several button states over a shared serial connection, reducing the number of ESP32 pins needed. The firmware reads these states and the rotary encoder, then applies chord selections, quality modifiers, octave changes, sound presets, volume or mute.

### Harmony

The harmony code takes a scale position, root pitch, voicing level, bass option and quality override, then produces a bounded list of notes. Pitches use MIDI note numbers. This calculation is separate from the button scanning and audio calls, so it can be tested on a computer without flashing the board.

### Sound

The C/C++ firmware sends note events to AMY, the synthesis engine. AMY turns the notes into audio, and I2S, a digital audio connection, carries the output to the audio hardware.

Selecting a new chord releases the previous chord before starting its replacement. Several notes can sound within a chord, but independently held chords do not overlap. This places a bound on the number of active notes.

The firmware stores the exact notes it started and the button that owns them. It releases those stored pitches when the chord changes. Releasing an older button therefore does not stop a newer chord, and an octave or quality change does not alter which old notes need to stop.

## Current version

The control-surface firmware includes chord buttons, quality modifiers, octave and preset changes, plus encoder-based volume and mute. OLED display work exists in earlier versions but is not part of this version.

Host-side tests check note counts and pitch ranges. Rapid encoder changes, simultaneous presses and audible note releases still need checks on the current device. Button handling uses an elapsed-time filter; requiring a stable input for a full interval is a remaining improvement. A current hardware demo is pending.

Full firmware, harmony tables and hardware designs remain private.
