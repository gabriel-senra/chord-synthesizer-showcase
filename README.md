# Chord Synthesizer

This is an ESP32-based instrument that plays complete chords from physical buttons. Instead of playing every note separately on a keyboard, the player selects a chord and changes its octave, sound or harmonic character through the controls.

For example, a C major seventh chord contains C, E, G and B; adding D makes a C major ninth. The firmware supports these richer chord types as simultaneous notes. In the control-surface version described here, pressing a new chord replaces the previous one.

The firmware is written in C/C++. **AMY**, the synthesis engine, turns note events into sound. **I2S**, a digital audio connection, carries the output from the ESP32 to the audio hardware.

## Engineering challenges and solutions

### Connecting several controls with limited input pins

A microcontroller has a limited number of general-purpose input/output pins, or **GPIOs**. Giving every button its own pin would quickly use them up.

The control surface uses chained **input shift registers**. These chips capture several button states and send them back through a shared serial connection. The firmware reads those states, decodes the rotary encoder and applies the resulting musical actions.

This saves pins, but the firmware still has to poll frequently enough to capture encoder movement. Fast control changes need testing on the physical build.

### Playing extended chords without accumulating too many voices

A **voice** is a sounding note that consumes synthesis resources. A ninth chord requires more simultaneous notes than a simple three-note chord. Sustaining several extended chords independently could quickly increase the resource demand.

The firmware limits harmony complexity and keeps one chord active at a time. Several notes sound together within that chord, but a new chord first releases the old one. This keeps resource use and note ownership manageable, with the tradeoff that independently held chords do not overlap in this version.

### Preventing notes from being left sounding after a change

Changing a chord's octave or quality changes the pitches that must be released later. Recomputing the old chord from new settings would risk sending note-off events for the wrong pitches.

The firmware retains the list of notes it started. It stops those exact notes before starting the replacement harmony. It also records which button owns the sounding chord, so releasing an older button does not stop a newer chord.

This gives note-on and note-off events a clear lifecycle. Audible releases, rapid changes and simultaneous presses still need checking on the current device.

### Testing harmony without needing the hardware

The harmony calculations are separate from button scanning and AMY calls. They take a scale position, root pitch, voicing level, bass option and quality override, then produce a bounded list of notes.

That separation lets host-side tests check note counts and MIDI pitch ranges without flashing a board. **MIDI note numbers** represent pitches numerically; this does not mean the instrument requires an external MIDI keyboard. Host tests establish properties of the calculations, while sound and timing still need device tests.

## Current status

The control-surface firmware includes chord buttons, quality modifiers, octave and sound-preset changes, plus encoder-based volume and mute. Earlier versions also contain OLED display work; the display is not part of the version described here.

Button handling currently filters changes by elapsed time. A filter that requires the input to remain stable for a full interval is a separate improvement. That work, rapid-encoder testing and a current hardware demonstration remain outstanding.

Full firmware, harmony tables and hardware product designs remain private.
