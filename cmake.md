# Migrating to CMake

<br />

* CMake build system for ndn-cxx pushed to gerrit

  * Library and all unit tests work fine
  * Most options of existing waf build system are also implemented for cmake
* Partial CMake build system for NFD pushed to gerrit

  * The `nfd` binary works, other tools and unit tests are a work in progress
* Extra

  * Renamed `ndncatchunks` to `ndnget`
  * Renamed `ndnputchunks` to `ndnserve`

