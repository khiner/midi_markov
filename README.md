# MIDI Markov

A script that takes a MIDI file, or directory containing MIDI files, and outputs a MIDI file generated from a Markov chain of its events.

## Usage
```
$ bundle install
$ brew install timidity
$ ./midi_markov # defaults to using the entire midi_files directory as input. May take a little while (30 seconds or so)
```

## Options
```
$ ./midi_markov --help                                                        
Usage: midi_markov [options]
    -i, --input-path [FILE]          Path of MIDI file or directory to recursively search for MIDI files for training the Markov model
    -o, --output-path [OUTPUT_PATH]  Path to output generated MIDI file
    -l, --length [LENGTH]            Length of the generated piece in MIDI event groups (single notes or chords)
        --match-length [MATCH_LENGTH]
                                     Minimum length of sequences to match from input MIDI at each Markov step
        --match-delta                Require generated notes to make delta_times as well as note values at each Markov step
        --originality [ORIGINALITY]  A higher value (close to 1) will select Markov chain values that lead to more possible choices (to avoid long unique sequences in the original piece)
        --rand-seed [RAND_SEED]      Specify the seed used in the randomization of the generated pieces (to reproduce the exact same midi)
        --group-deltas               Force all simultaneous notes to ge grouped together (uses the delta from the first note in the chord as the chord duration)
        --on-delta [ON_DELTA]        This value will be used for the delta_time value of each 'on' note.  If not provided, values will be derived from source notes.
        --off-delta [OFF_DELTA]      This value will be used for the delta_time value of each 'off' note.  If not provided, values will be derived from source notes.
        --drone                      Introduce a little bug that makes things drone-y
```

[More thoughts](https://karl-hiner.squarespace.com/blog/2016/8/27/creating-harmonically-rich-drones-from-midi-files)
