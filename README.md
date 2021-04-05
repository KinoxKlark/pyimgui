[![completion](https://img.shields.io/badge/completion-70%25%20%28518%20of%20735%29-blue.svg)](https://github.com/swistakm/pyimgui)
[![Coverage Status](https://coveralls.io/repos/github/swistakm/pyimgui/badge.svg?branch=master)](https://coveralls.io/github/swistakm/pyimgui?branch=master)
[![Documentation Status](https://readthedocs.org/projects/pyimgui/badge/?version=latest)](https://pyimgui.readthedocs.io/en/latest/?badge=latest)

Builds:

* [![Build status](https://ci.appveyor.com/api/projects/status/s7pud6on7dww89iv?svg=true)](https://ci.appveyor.com/project/swistakm/pyimgui) (Windows)
* [![Build Status](https://travis-ci.com/swistakm/pyimgui.svg?branch=master)](https://travis-ci.com/github/swistakm/pyimgui/branches) (OS X & Linux)


# pyimgui

Python bindings for the amazing
[dear imgui](https://github.com/ocornut/imgui) C++ library - a Bloat-free
Immediate Mode Graphical User Interface.

Documentation: [pyimgui.readthedocs.io](https://pyimgui.readthedocs.io/en/latest/index.html)

# ImPlot

This fork includes python bindings for the amazing
[ImPlot](https://github.com/ocornut/imgui), GPU accelerated plotting library for
[Dear ImGui](https://github.com/ocornut/imgui).


        Warning: This is alpha quality code.

Have a look at the `doc/examples/integrations_glfw3_implot.py` that includes ImPlot
demo. Note that the demo was **NOT** ported to python yet.

It is not clear to me if this is the best place for ImPlot python binding. The
question remains if it would be better to place this code into a separate project,
or provide it as part of pyimgui. As of now the latter has been chosen due to my
laziness, poor undestanding of cython and also due to some obstacles
encountered during the process of creating this binding.

Read on if you are interested in couple of details.

As per ImGui and ImPlot documentation using them both as shared library is not recomended.
In case of python binding this is exactly what is being done. The ImGui part ends up being
a shared library and plot becomes a separate shared library. Creating a single shared library
(with ImGui and ImPlot) does not sound like a good idea at all so lets not go there. Not going
with shared library is also something that we can not do; AFAICT due to the way cypthon does
its business (I might be wrong).

At the level of C++ that cython wraps into python module and functions, ImPlot want access
to `imgui.h` and `imgui_internal.h`. For example ImPlot uses `ImTextureID`, `ImGuiCond`,
`ImGuiMouseButton`, `ImGuiKeyModFlags`, `ImGuiDragDropFlags`, `ImU32`, `ImDrawList`, `ImGuiContext`,
`ImVec2`, `ImVec4` and alike. These need to be exposed a cython level, too. Currently,
these come from `cimgui` and `internal` modules provided by pyimgui binding. Using them is
as simple as adding a `cimport` line to a `cimplot.pxd`. pyimgui (c)imports were requiered for
`plot.pyx` as well:

        cimport cimplot
        cimport cimgui
        cimport core
        cimport enums

If the ImPlot is placed in a separate project/repo these would need to be redefined.


When I tried to compile the ImPlot C++ code for cython binding (without adding ImGui sources)
for that shared library, doing `import imgui` in a user script fails with `GImGui` being undefined.
Have I missed something there during the library build?! I don't know.

If the ImPlot binding is separate from ImGui binding (cleanest approach), I'm not sure how
the user script would behave if pyimgui and pyimplot would be based of different ImGui C++ code.
Might be a non-issue, but I just do not know.

Have any ideas or suggestions on the above topics? Bring them up, please!



# Installation

**pyimgui** is available on PyPI so you can easily install it with `pip`:

    pip install imgui[full]

Above command will install `imgui` package with additional dependencies for all
built-in rendering backend integrations (pygame, cocos2d, etc.). If you don't
want to install all additional dependencies you can always use bare
`pip install imgui` command or select a specific set of extra requirements:

* for pygame backend use `pip install imgui[pygame]`
* for GLFW3 backend use `pip install imgui[glfw]`
* for SDL2 backend use `pip install imgui[sdl2]`
* for Cocos2d backend use `pip install imgui[cocos2d]`
* for pyglet backend use `pip install imgui[pyglet]`

Package is distributed in form of *built wheels* so it does not require
compilation on most operating systems. For more details about compatibility
with diffferent OSes and Python versions see the *Project ditribution*
section of this documentation page.


# Project status

The `imgui` package provides support for the majority of core ImGui widgets and
functionalities. Some low-level API elements and complex widgets (like plots)
may be missing. We are working hard to provide 100% feature mapping of the core
ImGui library. The *completion badge* shows up-to-date status of that goal.


# Project distribution

This project has a working build pipeline on Appveyor and Travis. It builds
succesfully for all major operating systems with different architectures:

* Windows (32bit & 64bit)
* Linux (32bit & 64bit)
* OS X (universal build)

Right now we are ready to shipping the built wheels for these three systems
(even for Linux using `manylinux1` wheels). The build pipeline covers multiple
Python versions:

* py27
* py33 (macOS wheel not available on PyPI)
* py34
* py35
* py36
* py37
* py38
* py39


**pyimgui** provides documentation with multiple visual examples.
Thanks to custom Sphinx extensions we are able to render GUI examples off
screen directly from docstring snippets. These examples work also as automated
functional tests. Documentation is hosted on
[pyimgui.readthedocs.io](https://pyimgui.readthedocs.io/en/latest/index.html).

If none of these wheels work in your environment you can install the `imgui`
package by compiling it directly from sdist distribution using one of following
commands:

    # will install Cython as extra dependency and compile from Cython sources
    pip install imgui[Cython] --no-binary imgui

    # will compile from pre-generated C++ sources
    pip install imgui --no-binary imgui


# Development tips
We have tried hard to make the process of bootstrapping this project as simple
as possible.

In order to build and install project locally ,ake sure you have created and
activated virtual environment using `virtualenv` or `python -m venv` (for newer
Python releases). Then you can just run:

    make build

This command will bootstrap whole environment (pull git submodules, install
dev requirements etc.) and build the project. `make` will automatically install
`imgui` in the *development/editable* mode. Then you can run some examples
found in the `doc/examples` directory in order to verify if project is working.

For building documentation and running tests you will need some additional
requirements from `doc/requirements-test.txt`.

You can run tests with:

    py.test


If you have any problems with building or installing the project just ask us
for help by creating GitHub issue.
