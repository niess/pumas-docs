# Installation

!!! note
    In order to include PUMAS in your project only two files are needed:
    [pumas.h](https://github.com/niess/pumas/blob/master/include/pumas.h) and
    [pumas.c](https://github.com/niess/pumas/blob/master/src/pumas.c).  In
    addition a C99 compliant compiler is required, e.g. *gcc* on UNIX or
    *Visual Studio* on Windows.
    {: .justify}

!!! warning
    It is assumed that the user already knows how to use a C compiler and how
    to install software from source on its own Operating System (OS). If not,
    please first check the apropriate documentation.
    {: .justify}

In the following we provide some specific details for building PUMAS as a
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
This will build PUMAS locally (_in-source_) as a shared library:
`lib/libpumas.so`.  In addition, the examples can be built in the `bin`
directory by further issuing `make examples`.
{: .justify}
</div>


<div markdown="1" class="shaded-box fancy">
## Building *libpumas* with *CMake*

The toolchain for building PUMAS can also be generated using CMake with the
provided
[CMakeLists.txt](https://github.com/niess/pumas/blob/master/CMakeLists.txt).
Instructions for running cmake on Windows and UNIX platforms are provided on the
[CMake website](https://cmake.org/runningcmake/). The following PUMAS specific
build options are listed below. Note that depending on your system some
options might not be available.
{: .justify}

| Option | Default | System | Description |
|--------|---------|--------|-------------|
|`PUMAS_BUILD_EXAMPLES`|`FALSE`| `ALL`  | Build the PUMAS examples. {: .justify}|
|`PUMAS_BUILD_TEST`    |`FALSE`| `LINUX`| Build the PUMAS test suite. {: .justify}|
|`PUMAS_USE_GDB`       |`FALSE`| `UNIX` | Enable specific options for debuging with `gdb`. {: .justify}|

In addition, the following standard CMake options might be relevant:
{: .justify}

| Option | Default | Description |
|--------|---------|-------------|
|`BUILD_SHARED_LIBS`   |`TRUE`        | Build PUMAS as a shared library. Set to `FALSE` in order to build PUMAS as a static library instead. {: .justify}|
|`CMAKE_BUILD_TYPE`    |`Release`     | Specify the build type, one of `Debug`, `Release`, `RelWithDebInfo` or `MinSizeRel`. {: .justify}|
|`CMAKE_INSTALL_PREFIX`|_OS dependent_| Prefix path for the installation of the PUMAS library and of the examples. |

For examples, on Unix systems the following builds and installs the PUMAS
library and the examples locally, _in-source_:
{: .justify}
```bash
# Clone the PUMAS library and set a build directory
git clone https://github.com/niess/pumas && mkdir pumas/build && cd pumas/build

# Configure the build with CMake for `in-source` installation
cmake .. -DCMAKE_INSTALL_PREFIX=$(pwd)/.. -DPUMAS_BUILD_EXAMPLES=true

# Compile and install PUMAS
make && make install
```

</div>
