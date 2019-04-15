[![Build Status](https://travis-ci.com/TheLartians/CPM.svg?branch=master)](https://travis-ci.com/TheLartians/CPM)

# CPM

CPM is a minimalistic package manager written in Cmake using `find_package` and `FetchContent` as a fallback to download non locally installed packages.

# Usage

To add a new dependency to your project simply add the Projects target name, the git URL and the version. If the git tag for this version does not match the pattern `v$VERSION`, then the exact branch or tag can be specified with the `GIT_TAG` argument. CMake options can also be supplied with the package.

```cmake
cmake_minimum_required(VERSION 3.14 FATAL_ERROR)

project(MyProject)

# add dependencies
include(${CMAKE_CURRENT_SOURCE_DIR}/cmake/CPM.cmake)

CPMAddPackage(
  NAME LarsParser
  GIT_REPOSITORY https://github.com/TheLartians/Parser.git
  VERSION 1.8
  GIT_TAG v1.8 # optional, as already defined by VERSION
  OPTIONS      # used to set CMake options in the inner package
    "LARS_PARSER_BUILD_GLUE_EXTENSION ON"
)

# add executable
set (CMAKE_CXX_STANDARD 17)
add_executable(my-project my-project.cpp)
target_link_libraries(my-project LarsParser)
```

# Adding CPM

To add CPM to your current project, simply include add `cmake/CPM.cmake` to your projects `cmake` directory. The command below will perform this automatically.

```bash
wget -O cmake/CPM.cmake https://raw.githubusercontent.com/TheLartians/CPM/master/cmake/CPM.cmake
```

# Supported packages

Basically any project that you can add via `add_subdirectory` should work with CPM.

# Options

If you set the CMake option `CPM_REMOTE_PACKAGES_ONLY` to `On`, packages will always be fetched via the URL. Setting `CPM_LOCAL_PACKAGES_ONLY` to `On` will only add packages via `find_package`.

# Advantages

- **Small repos** CPM takes care of package dependencies, allowing programmers to focus on creating small well-tested frameworks.
- **Cross-Plattform** CPM adds projects via `add_subdirectory`, which is compatible with all cmake toolchains and generators. 
- **Reproducable builds** By using versioning via git tags it is ensured that a project will always be in the same state everywhere.
- **No installation required** No need to install any third-party package managers. Just copy the files from the CMake directory and you're good to go.

# Limitations

- **First version used** In diamond-shaped dependency graphs (e.g. `A` depends on `C`(v1.1) and `A` depends on `B` depends on `C`(v1.2)) the first added dependency will be used (in this case `C`@1.1).
- **No auto-update** To update a dependency, version numbers or git tags in the cmake scripts must be adapted manually.