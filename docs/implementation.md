# Implementation reference

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

## Button filtering and tests

Button handling uses an elapsed-time filter. Host-side tests check chord note counts and MIDI pitch ranges.

Full firmware, harmony tables and hardware designs remain private.
