# emilib
This is a loose collection of C++14 libraries I tend to reuse between different (mostly game-related) projects.

They are meant mostly for me (Emil Ernerfeldt), but if you find them useful, have at it.

Their only dependencies are either system dependencies (pthread etc) and [Loguru](https://github.com/emilk/loguru). gl_lib also depends on OpenGL and GLEW. The libraries does not depend on each other (with an exception for ShaderMngr).

They all work on OSX and iOS and probably Linux. Some may work elsewhere.

# License
This software is in the public domain. Where that dedication is not
recognized, you are granted a perpetual, irrevocable license to copy
and modify this file as you see fit.

# Usage
Each library in emilib is stand-alone, so you can, for instance, use the coroutine library without having to compile anything but that.

Some are header-only, but most come with a `.cpp` file too.

To use a single library, just `#include` the header, and if there is a `.cpp` (or `.mm` in case of iOS/OSX) either compile and link that, or just include that too, e.g.:

	#include <emilib/coroutine.hpp>
	#include <emilib/coroutine.cpp> // Do this only in one of your .cpp files

You can also make use of `unit_build.cpp` to compile all of emilib all at once. To use the iOS/OSX specific libraries, use `unit_build.mm` instead.

You also need to compile Loguru. Luckily, `loguru.hpp` is included in this repo, and to compile it you just need to add the following to one of your .cpp files:

	#define LOGURU_IMPLEMENTATION
	#include <loguru.hpp>

Make sure you compile with -std=c++11 -lpthread -ldl

### Documentation
This file (README.md) contains an overview of each library. Read the header for each library to learn more.

### Examples
There is a very limited set of examples in the `examples/` folder.

### Tests
There is a very limited set of tests in the `tests/` folder.

# Libraries

### configuru.hpp
This is not part of emilib, but a copy of https://github.com/emilk/configuru here only for convenience.

### coroutine.hpp/.cpp
This is a "fake coroutine" class which implements a cooperative thread and methods for passing execution between the outer and inner thread.

This is really nice for handling things that you would normally use a state machine for. Perfect for games where you might want to have a scripted event, a dialogue or something else running in its own thread, but not at the same time as the main game logic thread.

### dir_watcher.hpp/.cpp
This library allows you to watch for changes in a directory, e.g. new files, deleted files or changes in existing files. This is great for doing automatic hot-reloading of things like textures, shaders, etc.

POSIX systems only.

### filesystem.hpp/.cpp
Contains functions for easily reading/writing files, getting file size and modified time, listing all files in a directory, etc.

It also contains functions for working with file paths.

### gl_lib
Code for doing simple things in OpenGL and/or OpenGL ES (GLES). It has classes and methods for dealing with:

* 2D Textures
* Vertex and fragment shaders
	* You can use the same shader code for GLES and non-GLES targets
* Vertex buffers (VBO/VAO)
* Viewports
* Off-screen buffers (FBO)

Depends on OpenGL and GLEW.

Include `gl_lib.hpp` and link with `gl_lib.cpp`. You can also make use of `gl_lib_fwd.hpp` to bring in forward declarations for most things in gl_lib. To do OpenGL calls you can include `gl_lib_opengl.hpp` (which just includes the correct OpenGL and/or glew headers for you system).

### hash_map.hpp / hash_set.hpp
Cache-friendly hash map/set with open addressing, linear probing and power-of-two capacity. Acts mostly like `std::unordered_map/std::unordered_set` except for:

* Much better performance
* Key/values may move when rehashing

### irange.hpp
Simple integer range, allowing you to replace `for (size_t i = 0; i < limit; ++i)` with `for (auto i : irange(limit))`.
It also provides a `Range` class which represent an integer range in a half-closed interval [begin, end) as well as the following functions:

	for (const auto ix : irange(end))                        { CHECK_F(0     <= ix && ix < end);           }
	for (const auto ix : irange(begin, end))                 { CHECK_F(begin <= ix && ix < end);           }
	for (const auto ix : indices(some_vector))               { CHECK_F(0 <= ix && ix < some_vector.size(); }
	for (const char ch : emilib::cstr_range("hello world!")) { ...                                         }
	for (auto& value : it_range(begin, end))                 { std::cout << value;                         }

### list_map.hpp / list_set.hpp
Simple O(N) map/set with small overhead and great performance for small N.

### loguru.hpp
This is not part of emilib, but a copy of https://github.com/emilk/loguru here for convenience, and as a dependency for most other libraries.

### mem_map.hpp/.cpp
Simple wrapper around memory mapping with RAII.

### music.hpp/.mm
Stream mp3 music on OSX and iOS.

### movement_tracker.hpp/.cpp
Track movement of some data, e.g. to estimate velocity from recent movement.
For instance, you can use this to track a finger flicking something on a touch-screen to calculate the final velocity when the finger is released.

### pvr.hpp/.mm
iOS only: loading of compressed .pvr textures. Depends on gl_lib.

### shader_mngr.hpp/.cpp
This is NOT a stand-alone library, but depends on other parts of emilib as well as loguru and configuru.
ShaderMngr's job is to encapsulate loading, memoization and reloading of shader files.
In particular, ShaderMngr can detect changes in .shader files and automatically reload (hot-reloading) them.

### string_interning.hpp/.cpp
Stupid simple thread-safe string interning.

### strprintf.hpp/.cpp
Minimalistic string formating library. Provides two functions: `strprintf` and `vstrprintf` that act like `printf` and `vprintf` respectively, but return the formated text as a `std::string` instead of printing it.

### text_paint.hpp/.mm
Pretty multi-line Unicode text rendering for OSX and iOS. It lets you select font, size, alignment, wrapping size and allows you to colorize sub-ranges of the text.

It draws the text to memory, so it is up to you to display it (e.g. upload it to a texture).

### texture_mngr.hpp/.cpp
This is NOT a stand-alone library, but depends on other parts of emilib as well as loguru.
TextureMngr's job is to encapsulate loading, memoization and reloading of texture files.
In particular, TextureMngr can detect changes in image files and automatically reload (hot-reloading) them.

### tga.hpp/.cpp
Dump a tga image to disk.

### timer.hpp/.cpp
Monotonic wall time chronometer.

### tuple_util.hpp
Adds `for_each_tuple` for iterating over a `std::tuple` and also overloads `std::hash` for `std::tuple`.

### utf8.hpp/.cpp
Really basic utf8 operations.

### wav.hpp/.cpp
Parse WAVE (.wav) sound files.
