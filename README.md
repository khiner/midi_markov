# MIDI Markov

A script that takes a MIDI file, or directory containing MIDI files, and outputs a MIDI file generated from a Markov chain of its events.

## Usage
```
$ git clone git@github.com:khiner/midi_markov.git
$ cd midi_markov
$ gem install bundler
$ bundle install
$ brew install timidity
$ ./midi_markov # defaults to using the entire midi_files directory as input. May take a little while (30 seconds or so)
```

## Options
```
Usage: midi_markov [options]
    -i, --input-path [FILE]          Path of MIDI file or directory to recursively search for MIDI files for training the Markov model
    -o, --output-path [OUTPUT_PATH]  Path to output generated MIDI file
    -l, --length [LENGTH]            Length of the generated piece in MIDI event groups (single notes or chords)
    -r, --rand-seed [RAND_SEED]      Specify the seed used in the randomization of the generated pieces (to reproduce the exact same midi)
        --match-length [MATCH_LENGTH]
                                     Minimum length of sequences to match from input MIDI at each Markov step
        --match-deltas               Require generated notes to make delta_times as well as note values at each Markov step
        --group-deltas               Force all simultaneous notes to ge grouped together (uses the delta from the first note in the chord as the chord duration)
    -n, --on-delta [ON_DELTA]        This value will be used for the delta_time value of each 'on' note.  If not provided, values will be derived from source notes.
    -f, --off-delta [OFF_DELTA]      This value will be used for the delta_time value of each 'off' note.  If not provided, values will be derived from source notes.
    -g, --originality [ORIGINALITY]  A higher value (close to 1) will select Markov chain values that lead to more possible choices (to avoid long unique sequences in the original piece)
    -d, --drone                      Introduce a little bug that makes things drone-y
```

### rand-seed option
`rand-seed` is a way to produce identical results with multiple runs of the program.  `rand-seed` (an integer) is used for all of the nondeterministic aspects of the algorithm, and can be thought of as the address of the generated piece within the program.  If you produce a piece you like, this repo along your `rand-seed` value is all that is needed to reproduce it exactly!

If no `rand-seed` value is provided, a random one is used and the first output line you will see after running will display the value used (e.g. "Using rand-seed value of 7675").

### match-length option
`match-length` is the number is previous notes or chords in the generated sequence that must be matched when finding the next note or chord.

For example, consider the Markov map created from Beethoven's 5th:

![](https://upload.wikimedia.org/wikipedia/commons/thumb/6/6f/Beethoven_symphony_5_opening.svg/2000px-Beethoven_symphony_5_opening.svg.png)

This would be what the Markov map would look like if `match-length` were set to `1`: 

```
G => (G, G, E) # since a single G is followed by two Gs and one E in the sequence
E => (F)
F => (F, F, D)
D => ()
```

If the last note played in the sequence were an F, for the next note we are twice as likely to choose another F as we are a D.

However, if `match-length` were set to `2`, the keys in the map would be two note sequences:

```
[G,G] => (G, E) # since two consecutive Gs are followed by one G and one E in the sequence
[G,E] => (F)
[E,F] => (F)
[F,F] => (F,D)
[F,D] => (D)
[F,F] => (F, D)
[F,D] => ()
```

Finally, if `match-length` were `3`, the keys would be three-note sequences:

```
[G,G,G] => (E) # since two consecutive Gs are followed by one G and one E in the sequence
[G,G,E] => (F)
[G,E,F] => (F)
[E,F,F] => (F)
[F,F,F] => (D)
[F,F,D] => ()
```

This demonstrates how higher `match-length` values result in fewer choices at each step (with `match-length=3`, we only have one choice at each step!), and can result in passages that follow a specific sequence in a piece until reaching a branch at a common sequence that occurs in another piece.


### match-deltas option

`match-deltas` puts further restrictions on what is considered a sequence-match by including both the note value and the note duration in the Markov map key.

Specifying this option will generally result in pieces that more closely resemble the source material and will tend to sound more musical, but will also be less original.


### group-deltas option

`group-deltas` forces notes that occurred together in the original piece to always play simultaneously.


### on/off-delta options

`on-delta` or `off-delta` can be used to specify exact delta values (durations) for on or off events, rather than deriving those values from the source material.  The values given will be used for every note, resulting in a rhytmically uniform generated piece.


### originality option

`originality` is a value between 0 and 1 that causes the Markov generator to make choices at each step that will minimize (close to 0) or maximize (close to 1) the options that will be available at the next step.

Values close to 0 will tend to follow unique passages in the source material, while values towards 1 tend to create meandering, mostly single-note melodies, since specific single notes are more common than specific chords, and thus lead to more choices.


### drone option

`drone` is a little bug I decided to keep in which prevents notes from starting and ending on the same channel, resulting in stacked notes that start but don't always get stopped.  It can create some slow-moving ambient tones.

[More thoughts](https://karl-hiner.squarespace.com/blog/2016/8/27/fun-generating-midi-with-markov-chains)
