# Chord Synthesizer

An ESP32 instrument for playing full chords from physical buttons. Choose a chord, change its character and explore the sound without playing every note separately on a keyboard.

**Built with:** C/C++, ESP32 and AMY

## What you can play

A C major seventh contains **C, E, G and B**. Add **D** and it becomes a C major ninth. The instrument plays those notes together from a chord selection.

- Select a chord with the buttons.
- Change its quality or octave.
- Choose a sound preset.
- Adjust volume or mute with the rotary encoder.

One chord sounds at a time. Selecting another releases the previous chord and starts the new one; the notes within each chord sound together.

## From a button press to sound

**Read the controls.** Input chips collect several button states through a shared connection, so each button does not need its own ESP32 pin. The firmware reads those states alongside the encoder.

**Choose the notes.** The harmony code combines the selected scale position, root pitch, voicing and modifiers into a list of notes. This part runs separately from the physical controls and can be tested on a computer.

**Play the chord.** AMY, the synthesis engine, turns note events into sound. A digital audio connection carries the output from the ESP32 to the audio hardware.

**Release the right notes.** The firmware remembers the pitches it started and the button that owns them. Changing the octave does not lose track of the old notes, and releasing an older button does not stop a newer chord.

Keeping one active chord bounds the number of sounding notes and gives each button press a clear musical result.

## Tests

Firmware tests run on a computer and check chord note counts and pitch ranges.

## Further reading

[Controls, harmony, note handling and testing](docs/implementation.md)

Full firmware, harmony tables and hardware designs remain private.
