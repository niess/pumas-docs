# Installation

!!! note
    In order to include PUMAS in your project only two files are needed:
    [pumas.h](https://github.com/niess/pumas/blob/master/include/pumas.h) and
    [pumas.c](https://github.com/niess/pumas/blob/master/src/pumas.c).  In
    addition a C99 compliant compiler is required, e.g. *gcc* on UNIX or
    *Visual Studio* on Windows.
    {: .justify}

In the following we provide detailed instructions for building PUMAS as a
standalone library as well as for building the examples.
{: .justify}


<div markdown="1" class="shaded-box fancy">
## Building *libpumas* with *make*

On Linux or OSX you might directly use the provided
[Makefile](https://github.com/niess/pumas/blob/master/Makefile). E.g., by
cloning the PUMAS repository as:
{: .justify}
```bash
git clone https://github.com/niess/pumas && cd pumas && make
```
This will build PUMAS as a shared library (in `lib/libpumas.so`).
In addition, the examples can be built in the `bin` directory by further
issuing `make examples`.
{: .justify}
</div>


<div markdown="1" class="shaded-box fancy">
## Building *libpumas* with *CMake*

The toolchain for building PUMAS can also be generated using CMake with the
provided
[CMakeLists.txt](https://github.com/niess/pumas/blob/master/CMakeLists.txt).
Instructions for running cmake on Windows and UNIX platforms are provided on the
[CMake website](https://cmake.org/runningcmake/). The following PUMAS specific
build options are available:
{: .justify}

| Option | Default | Description |
|--------|---------|-------------|
|`BUILD_SHARED_LIBS`   |`TRUE` |Build PUMAS as a shared library. Set to `FALSE` in order to build PUMAS as a static library instead. {: .justify}|
|`PUMAS_BUILD_EXAMPLES`|`FALSE`|Build the examples under `bin`. {: .justify}|
|`PUMAS_BUILD_TEST`    |`FALSE`|Build the test suite. {: .justify}|
|`PUMAS_USE_GDB`       |`FALSE`|Enable specific options for debuging with `gdb` on UNIX systems. {: .justify}|
</div>
