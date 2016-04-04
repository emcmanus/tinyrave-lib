# TinyRave Libraries

A collection of libraries available to TinyRave tracks. All libraries can be
imported in either CoffeeScript and JavaScript tracks.


### Adapter.js

This script is prepended to every TinyRave track for communication between the
track's web worker instance and the TinyRave host.

**Import with:** _This is included in every track by default._

[Source]()


### StdLib.coffee

Contains:

Sample-accurate implementation of `setInterval` / `setTimeout` / `clearInterval`
defined as functions on the TinyRave object.

Notes. 8 octaves of them, defined as values in Hz on the object `Frequency`.

Extension to Number that provides a `beat()`/`beats()` function, allowing you to
pass beats to functions that expect seconds (E.g. the timing functions above.)
Use TinyRave.setBPM(newBPM) to set the value used in conversion.

A small DSL for building "blocks" - i.e. sections of a song. For example, you
may create an intro block, a verse block, and a chorus block. The scheduler will
take care of transitioning between blocks. During development you can comment
out blocks to focus on just a single section of your track.

_Note:_ This library does not contain any abstractions for making tones or
mixing signals. Check out instruments.coffee for that.

**Import with:** _This is included in every track by default._ If you'd like an
option to opt out, let me know and I'll build it.

**Usage (JavaScript)**

```
// Frequencies
Frequency.A_4           // 440.0

// Beats
TinyRave.setBPM(120)    // Must set!
4.beats()   // 3 seconds
1.beat()    // 0.5 seconds

// Low-level API
intervalId  = TinyRave.setInterval(myFunction1, 300)
timeoutId   = TinyRave.setTimeout(myFunction2, 300)
TinyRave.clearInterval(intervalId)
TinyRave.clearInterval(timeoutId) // Timeouts can be preempted

// High-level API - this extends an internal block class
intro = TinyRave.createBlock( 12,
  blockWillStart: function(){
    this.someVar = 1;
  },
  blockDidEnd: function(){
    // Clean up after your block here
    // Note after() and every() calls are cleared for you
  },
  run: function(){
    this.every(0.05, function(){
      this.someVar++;
    });
    this.after(0.2, function(){
      console.log("someVar value: " + this.someVar);
      doSomething()
    });
    this.every(0.5, function(){
      exampleDrumLoop();
      this.after(0.2, function(){
        // Will be called every 0.5s starting at 0.2s
        timersCanBeNested();
      });
    });
  }
)
TinyRave.scheduler.push(intro)
```

**Usage (CoffeeScript)**

```
# Frequencies
Frequency.A_4           # 440.0

# Beats
TinyRave.setBPM(120)    # Must set!
4.beats()               # 3 seconds
1.beat()                # 0.5 seconds

# Low-level API
intervalId  = TinyRave.setInterval(myFunction1, 300)
timeoutId   = TinyRave.setTimeout(myFunction2, 300)
TinyRave.clearInterval(intervalId)
TinyRave.clearInterval(timeoutId) # Timeouts can be preempted

# High-level API - this extends an internal block class
intro = TinyRave.createBlock( 36.beats(),
  blockWillStart: ->
    @count = 0
  run: ->
    @every 0.1, ->
      @count++
    @every 1, ->
      console.log "Count is #{@count}"
)

TinyRave.scheduler.push(intro)
```

[Source]()
[Example track]()


### Instruments.coffee

Basic oscillators, a biquad filter and more. Check the source.

**Import with:**

```
importScripts('')
```

[Source]()
[Example track]()