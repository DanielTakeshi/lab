# Setup

- I installed bazel locally. 
- I created a Python 3.6 virtualenv and did `pip install numpy` there.
- I cloned deepmind/lab and then changed `python.BUILD` to use Python 3.6 instead of Python 3.5 wherever it said `3.5`.

All of these commands below work in that they don't give errors on my Ubuntu 18 machine:

```
# Build the Python interface to DeepMind Lab
lab$ bazel build -c opt //:deepmind_lab.so

# Build and run the tests for it
lab$ bazel test -c opt //python/tests:python_module_test

# Run a random agent
lab$ bazel run -c opt //:python_random_agent
```

*The Python module test originally said "one test failed locally"* but I think it was because I was not in my virtualenv or something like that. When I run it now, it succeeds.

Running their random agent with the visual seems to work:

```
(deepmindlab) daniel@takeshi:~/lab (master) $ bazel run :python_random_agent --define graphics=sdl --     --length=10000 --width=640 --height=480
DEBUG: Rule 'rules_cc' indicated that a canonical reproducible form can be obtained by modifying arguments sha256 = "f977d49a6fb605964661d65c73b930a595f55f1be63d637535e52263a8a5159c"
DEBUG: Call stack for the definition of repository 'rules_cc' which is a http_archive (rule definition at /home/daniel/.cache/bazel/_bazel_daniel/c24a65d157cf81f207b2d30cca7c57fc/external/bazel_tools/tools/build_defs/repo/http.bzl:262:16):
 - /home/daniel/lab/WORKSPACE:11:1
DEBUG: Rule 'com_google_absl' indicated that a canonical reproducible form can be obtained by modifying arguments sha256 = "347871707ce24c690e9ffe7d2b13464a4a4bd382c6621dc3e452ef79d9990af4"
DEBUG: Call stack for the definition of repository 'com_google_absl' which is a http_archive (rule definition at /home/daniel/.cache/bazel/_bazel_daniel/c24a65d157cf81f207b2d30cca7c57fc/external/bazel_tools/tools/build_defs/repo/http.bzl:262:16):
 - /home/daniel/lab/WORKSPACE:17:1
INFO: Analyzed target //:python_random_agent (0 packages loaded, 0 targets configured).
INFO: Found 1 target...
Target //:python_random_agent up-to-date:
  bazel-bin/python_random_agent
INFO: Elapsed time: 0.131s, Critical Path: 0.00s
INFO: 0 processes.
INFO: Build completed successfully, 1 total action
INFO: Build completed successfully, 1 total action
Starting random spring agent. Action spec: [{'min': -512, 'max': 512, 'name': 'LOOK_LEFT_RIGHT_PIXELS_PER_FRAME'}, {'min': -512, 'max': 512, 'name': 'LOOK_DOWN_UP_PIXELS_PER_FRAME'}, {'min': -1, 'max': 1, 'name': 'STRAFE_LEFT_RIGHT'}, {'min': -1, 'max': 1, 'name': 'MOVE_BACK_FORWARD'}, {'min': 0, 'max': 1, 'name': 'FIRE'}, {'min': 0, 'max': 1, 'name': 'JUMP'}, {'min': 0, 'max': 1, 'name': 'CROUCH'}]
Finished after 10000 steps. Total reward received is 0.000000
```

But the really weird thing is that I get these images *which show the text upside down*: 

![](IMG_5591_upside_down.JPG)

I hit the ESCAPE key to pause the game so it shows the screen. Otherwise the agent just moves randomly and it's distracting. It will automatically terminate after the maximum steps even if I pause it.


**(The rest of this README is from DeepMind)**

# <img src="/docs/template/logo.png" alt="DeepMind Lab">

*DeepMind Lab* is a 3D learning environment based on id Software's
[Quake III Arena](https://github.com/id-Software/Quake-III-Arena) via
[ioquake3](https://github.com/ioquake/ioq3) and
[other open source software](#upstream-sources).

<div align="center">
  <a href="https://www.youtube.com/watch?v=M40rN7afngY" target="_blank">
    <img src="http://img.youtube.com/vi/M40rN7afngY/0.jpg"
         alt="DeepMind Lab - Nav Maze Level 1"
         width="240" height="180" border="10" />
  </a>
  <a href="https://www.youtube.com/watch?v=gC_e8AHzvOw" target="_blank">
    <img src="http://img.youtube.com/vi/gC_e8AHzvOw/0.jpg"
         alt="DeepMind Lab - Stairway to Melon Level"
         width="240" height="180" border="10" />
  </a>
  <a href="https://www.youtube.com/watch?v=7syZ42HWhHE" target="_blank">
    <img src="http://img.youtube.com/vi/7syZ42HWhHE/0.jpg"
         alt="DeepMind Lab - Laser Tag Space Bounce Level (Hard)"
         width="240" height="180" border="10" />
  </a>
  <br /><br />
</div>

*DeepMind Lab* provides a suite of challenging 3D navigation and puzzle-solving
tasks for learning agents. Its primary purpose is to act as a testbed for
research in artificial intelligence, especially deep reinforcement learning.

## About

Disclaimer: This is not an official Google product.

If you use *DeepMind Lab* in your research and would like to cite
the *DeepMind Lab* environment, we suggest you cite
the [DeepMind Lab paper](https://arxiv.org/abs/1612.03801).

You can reach us at [lab@deepmind.com](mailto:lab@deepmind.com).

## Getting started on Linux

* Get [Bazel from bazel.io](https://docs.bazel.build/versions/master/install.html).

* Clone DeepMind Lab, e.g. by running

```shell
$ git clone https://github.com/deepmind/lab
$ cd lab
```

For a live example of a random agent, run

```shell
lab$ bazel run :python_random_agent --define graphics=sdl -- \
               --length=10000 --width=640 --height=480
```

Here is some [more detailed build documentation](/docs/users/build.md),
including how to install dependencies if you don't have them.

To enable compiler optimizations, pass the flag `--compilation_mode=opt`, or
`-c opt` for short, to each `bazel build`, `bazel test` and `bazel run` command.
The flag is omitted from the examples here for brevity, but it should be used
for real training and evaluation where performance matters.

### Play as a human

To test the game using human input controls, run

```shell
lab$ bazel run :game -- --level_script=tests/empty_room_test --level_setting=logToStdErr=true
# or:
lab$ bazel run :game -- -l tests/empty_room_test -s logToStdErr=true
```

Leave the `logToStdErr` setting off to disable most log output.

The values of observations that the environment exposes can be printed at every
step by adding a flag `--observation OBSERVATION_NAME` for each observation of
interest.

```shell
lab$ bazel run :game -- --level_script=lt_chasm --observation VEL.TRANS --observation VEL.ROT
```

### Train an agent

*DeepMind Lab* ships with an example random agent in
[`python/random_agent.py`](python/random_agent.py)
which can be used as a starting point for implementing a learning agent. To let
this agent interact with DeepMind Lab for training, run

```shell
lab$ bazel run :python_random_agent
```

The [Python API](/docs/users/python_api.md)
is used for agent-environment interactions.

*DeepMind Lab* ships with [different
levels](/docs/levels.md) implementing different
tasks. These tasks can be configured using Lua scripts, as described in the [Lua
API](/docs/developers/reference/lua_api.md).

-----------------

## Upstream sources

*DeepMind Lab* is built from the *ioquake3* game engine, and it uses the tools
*q3map2* and *bspc* for map creation. Bug fixes and cleanups that originate
with those projects are best fixed upstream and then merged into *DeepMind Lab*.

* *bspc* is taken from [github.com/TTimo/bspc](https://github.com/TTimo/bspc),
  revision d9a372db3fb6163bc49ead41c76c801a3d14cf80. There are virtually no
  local modifications, although we integrate this code with the main ioq3 code
  and do not use their copy in the `deps` directory. We expect this code to be
  stable.

* *q3map2* is taken from
  [github.com/TTimo/GtkRadiant](https://github.com/TTimo/GtkRadiant),
  revision d3d00345c542c8d7cc74e2e8a577bdf76f79c701. A few minor local
  modifications add synchronization. We also expect this code to be stable.

* *ioquake3* is taken from
  [github.com/ioquake/ioq3](https://github.com/ioquake/ioq3),
  revision 29db64070aa0bae49953bddbedbed5e317af48ba. The code contains extensive
  modifications and additions. We aim to merge upstream changes occasionally.

We are very grateful to the maintainers of these repositories for all their hard
work on maintaining high-quality code bases.

## External dependencies, prerequisites and porting notes

*DeepMind Lab* currently ships as source code only. It depends on a few external
software libraries, which we ship in several different ways:

 * The `zlib`, `glib`, `libxml2`, `jpeg` and `png` libraries are referenced as
   external Bazel sources, and Bazel BUILD files are provided. The dependent
   code itself should be fairly portable, but the BUILD rules we ship are
   specific to Linux on x86. To build on a different platform you will most
   likely have to edit those BUILD files.

 * Message digest algorithms are included in this package (in
   [`//third_party/md`](third_party/md)), taken from the reference
   implementations of their respective RFCs. A "generic reinforcement learning
   API" is included in [`//third_party/rl_api`](third_party/rl_api), which has
   also been created by the *DeepMind Lab* authors. This code is portable.

 * EGL headers are included in this package (in
   `//third_party/GL/{`[`EGL`](third_party/GL/EGL)`,`[`KHR`](third_party/GL/KHR)`}`),
   taken from the Khronos OpenGL/OpenGL ES XML API Registry at
   [www.khronos.org/registry/EGL](http://www.khronos.org/registry/EGL/). The
   headers have been modified slightly to remove the dependency of EGL on X.

 * Several additional libraries are required but are not shipped in any form;
   they must be present on your system:
   * SDL 2
   * gettext (required by `glib`)
   * OpenGL: A hardware driver and library are needed for hardware-accelerated
     human play. The headless library that machine learning agents will want to
     use can use either hardware-accelerated rendering via EGL or GLX or
     software rendering via OSMesa, depending on the `--define headless=...`
     build setting.
   * Python 2.7 (other versions might work, too) with NumPy, PIL. (A few tests
     require a NumPy version of at least 1.8.) Python 3 (at least 3.5) is
     supported experimentally.

The build rules are using a few compiler settings that are specific to GCC. If
some flags are not recognized by your compiler (typically those would be
specific warning suppressions), you may have to edit those flags. The warnings
should be noisy but harmless.
