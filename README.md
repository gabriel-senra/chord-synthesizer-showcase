# Chord Synthesizer

This ESP32 instrument uses AMY to play complete chords from physical buttons. It supports seventh- and ninth-chord construction, chord-quality modifiers, octave and patch changes, and volume control through a rotary encoder.

**C++ / C · ESP32 · AMY · I2S**

## How it works

The firmware scans buttons and an encoder, resolves the selected harmony, and sends note events to AMY. AMY produces the audio through I2S. In the control-surface version described here, several notes sound within one chord, but only one chord is active at a time.

## Engineering challenges and how they are handled

### Reading several controls without dedicating a GPIO to each one

The control surface has note, modifier and setting buttons as well as an encoder. Chained input shift registers let the firmware read these inputs through a small shared interface. The firmware turns the sampled bits into button states and encoder transitions before applying musical actions.

This reduces the GPIO requirement, but polling still needs to be frequent enough to capture encoder movement. Input behavior during rapid playing remains an area for hardware testing.

### Keeping richer chords within the synthesis budget

A single button can produce several simultaneous notes, and extensions add more voices. The firmware bounds the harmony complexity and replaces the active chord when a new chord is pressed. That keeps the note lifecycle manageable on the target board rather than allowing held chords to accumulate indefinitely.

The tradeoff is intentional: this version plays one multi-note chord at a time instead of sustaining several independently held chords.

### Releasing the correct notes after harmony changes

A chord may change because of a new button, an octave adjustment or a modifier. The firmware retains the active note list and explicitly stops those notes before starting the replacement harmony. Releasing a note button stops the chord only when that button owns the sounding chord.

The sequence avoids losing track of which notes need note-off events. Audible transitions, release behavior and simultaneous button presses still need to be checked on the current device.

### Turning musical settings into concrete note events

The harmony code is separate from the input scan and AMY event helpers. It takes the degree, root, voicing level, bass setting and quality override and produces a bounded note list. The firmware then emits note-on and note-off events for those pitches.

Keeping these responsibilities separate makes the harmony calculations testable on a host computer without an ESP32 or audio output. Host checks cover note-count bounds and MIDI range; they do not establish sound quality or hardware timing.

## Remaining work

The firmware includes time-gated button changes, but that is different from requiring an input to remain continuously stable before accepting it. Stable-interval debounce, rapid encoder changes and startup/release behavior need further hardware validation.

Earlier versions include OLED display work; that display is not part of the control-surface version described here. A current hardware demonstration will establish the exact configuration being shown.

Full firmware, harmony tables and product hardware designs remain private.
