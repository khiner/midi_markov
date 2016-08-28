# midi_markov

## MIDI Drone

A little script that takes a MIDI file and outputs a MIDI file with its note-end events dropped by an octave, creating a swelling drone-like effect as the notes continuously stack ontop of each other.

### Usage
```
$ bundle install
$ brew install timidity
$ ./midi_drone {midi_file_path} # generate the drone-y midi file
$ timidity drone.mid # listen!
```

[More thoughts](https://karl-hiner.squarespace.com/blog/2016/8/27/creating-harmonically-rich-drones-from-midi-files)
