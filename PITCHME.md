#HSLIDE
## How To Trace Running Programs

I program Python.
<br>
Why should I care about that low level stuff?

<!-- Often it is hard, to read the complete source code to find out what is going on, why something fails, etc. -->

* Might be helpful for finding problems:
* Often hard to read the complete source
* Observe what your program actually does, in *reality*.

#HSLIDE
## `strace`

* Why does Python use this setting?
* Should be a different one because I configured it there...
* Where is this configuration file?

* Which libraries are loaded when I call my Python script?
* Problem: `ldd` only shows the linked libraries, not the dynamically loaded ones.

```bash
    strace  python -c 'import numpy as np; a=np.ones(10); b=np.ones(10); d = a*b' 2> out
    grep  'lib.*.so.* = 0$' out
```

Changed a file, but no effect. Is it opened at all?

[Further Reading](http://hokstad.com/5-simple-ways-to-troubleshoot-using-strace)

#VSLIDE
### SSH Keys

I cannot connect via `ssh` with my generated key.
What key does `ssh` / `git` actually use?

### How does `which` work?

Is there any better possibility to find an executable than probing each directory in `PATH`?
No...

## `ltrace`

My program calls a library.
Which functions are really called?
How often?
Where is the actual code executed?


### `latrace`

Also observe inter-library calls.

    latrace -D -p  python -c 'import numpy as np; a=np.ones(10); b=np.ones(10); d = a*b' > out


What does `np.random.rand(10)` do?

    latrace -D python -c 'import numpy as np; np.random.rand(10)' > out
    grep -i random

--> use Python's `_PyRandom_Init` which opens `/dev/urandom` which is the
[kernel random number source device](https://linux.die.net/man/4/urandom).

## `ptrace`

See [Julia Evan's Blog](https://jvns.ca/blog/2016/06/12/a-weird-system-call-process-vm-readv/)

## `perf`

Interrupt the program in certain intervals (number of cycles).
What is it doing at this moment?

Man Page:

       Performance counters for Linux are a new kernel-based subsystem that
       provide a framework for all things performance analysis. It covers
       hardware level (CPU/PMU, Performance Monitoring Unit) features and
       software features (software counters, tracepoints) as well.

Therefore, you need to install

```bash
$ dpkg -S $(which perf)
linux-tools-common: /usr/bin/perf
```

and kernel modules.


### `perf top`

### Flame Graphs

Invented by [Brendan Gregg](http://www.brendangregg.com/FlameGraphs/cpuflamegraphs.html)
