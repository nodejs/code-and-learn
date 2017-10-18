I've only really looked at V8 (primarily the profiler) and a little bit of the SlowBuffer code in Node.js, so anything else would be interesting!
For example:
- the different libuv handle types (I've used async and timers before, but I'm not too clear on the other types)
- the event loop
- the garbage collector and the way some of the heap areas work (particularly the ones besides the new/old object areas)
- preloaded modules
- anything new/changed between 0.12.x and 4.x
