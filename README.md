# byte-lite: A single-file header-only C++17-like byte type for C++98, C++11 and later

[![Language](https://img.shields.io/badge/language-C++-blue.svg)](https://isocpp.org/)  [![Standard](https://img.shields.io/badge/c%2B%2B-98-blue.svg)](https://en.wikipedia.org/wiki/C%2B%2B#Standardization) [![Standard](https://img.shields.io/badge/c%2B%2B-11-blue.svg)](https://en.wikipedia.org/wiki/C%2B%2B#Standardization) [![Standard](https://img.shields.io/badge/c%2B%2B-14-blue.svg)](https://en.wikipedia.org/wiki/C%2B%2B#Standardization) [![Standard](https://img.shields.io/badge/c%2B%2B-17-blue.svg)](https://en.wikipedia.org/wiki/C%2B%2B#Standardization) [![License](https://img.shields.io/badge/license-MIT-blue.svg)](https://opensource.org/licenses/MIT) [![Build Status](https://travis-ci.org/martinmoene/byte-lite.svg?branch=master)](https://travis-ci.org/martinmoene/byte-lite) [![Build status](https://ci.appveyor.com/api/projects/status/gpmw4gt271itoy2n?svg=true)](https://ci.appveyor.com/project/martinmoene/byte-lite) [![Version](https://badge.fury.io/gh/martinmoene%2Fbyte-lite.svg)](https://github.com/martinmoene/byte-lite/releases) [![download](https://img.shields.io/badge/latest%20version%20%20-download-blue.svg)](https://raw.githubusercontent.com/martinmoene/byte-lite/master/include/nonstd/byte.hpp)

**Contents**
- [Example usage](#example-usage)
- [In a nutshell](#in-a-nutshell)
- [License](#license)
- [Dependencies](#dependencies)
- [Installation](#installation)
- [Synopsis](#synopsis)
- [Features](#features)
- [Reported to work with](#reported-to-work-with)
- [Building the tests](#building-the-tests)
- [Other implementations of byte](#other-implementations-of-byte)
- [Notes and references](#notes-and-references)
- [Appendix](#appendix)


Example usage
-------------

```Cpp
#include "byte.hpp"

#include <cassert>

using namespace nonstd;

int main()
{
    byte b1 = to_byte( 0x5a );  // to_byte() is non-standard, needed for pre-C++17
    byte b2 = to_byte( 0xa5 );

    byte r1 = b1 ^ b2; assert( 0xff == to_integer( r1 ) );  // not (yet) standard, needs C++11
    byte r2 = b1 ^ b2; assert( 0xff == to_integer<unsigned int>( r2 ) );
}
```

### Compile and run

```
prompt> g++ -std=c++11 -Wall -I../include/nonstd -o 01-basic 01-basic.cpp && 01-basic
```

Or to run with Buck:

```
prompt> buck run example:01-basic
```

In a nutshell
-------------
**byte lite** is a single-file header-only library to provide a [C++17-like distinct byte type](http://en.cppreference.com/w/cpp/types/byte) for use with C++98 and later.

**Features and properties of byte lite** are are ease of installation (single header), freedom of dependencies other than the standard library.

**A limitation of byte lite** is that you need to use function `to_byte(v)` to construct a `byte` from an intergal value `v`, when C++17's relaxation of the enum value construction rule is not available.

License
-------
*byte lite* uses the [MIT](LICENSE) license.


Dependencies
------------
*byte lite* has no other dependencies than the [C++ standard library](http://en.cppreference.com/w/cpp/header).


Installation
------------
*byte lite* is a single-file header-only library. Put `byte.hpp` in the [include](include) folder directly into the project source tree or somewhere reachable from your project.


Synopsis
--------

**Contents**
- [Types in namespace nonstd](#types-in-namespace-nonstd)
- [Algorithms for *byte lite*](#algorithms-for-byte-lite)
- [Configuration macros](#configuration-macros)

### Types in namespace nonstd

| Purpose            | Type            | Std     | Notes  |
|--------------------|:----------------|:-------:|:-------|
| Distinct byte type | enum class byte | >=C++17 | &nbsp; |
| &nbsp;             | struct byte     | < C++17 | &nbsp; |

### Algorithms for *byte lite*

| Kind              | Std     | Function | Result |
|-------------------|:-------:|----------|--------|
| Shift-assign      |  &nbsp; | template< class IntegerType ><br>constexpr byte & operator<<=( byte & b, IntegerType shift ) noexcept | left-shifted b |
| &nbsp;            |  &nbsp; | template< class IntegerType ><br>constexpr byte & operator>>=( byte & b, IntegerType shift ) noexcept | right-shifted b |
| Shift             |  &nbsp; | template< class IntegerType ><br>constexpr byte operator<<( byte b, IntegerType shift ) noexcept | left-shifted byte |
| &nbsp;            |  &nbsp; | template< class IntegerType ><br>constexpr byte operator>>( byte b, IntegerType shift ) noexcept | right-shifted byte |
| Bitwise-op-assign |  &nbsp; | template< class IntegerType ><br>constexpr byte & operator&#166;=( byte & l, byte r ) noexcept | bitwise-or-ed b |
| &nbsp;            |  &nbsp; | template< class IntegerType ><br>constexpr byte & operator&amp;=( byte & l, byte r ) noexcept | bitwise-xor-ed b |
| &nbsp;            |  &nbsp; | template< class IntegerType ><br>constexpr byte & operator&Hat;=( byte & l, byte r ) noexcept | bitwise-and-ed b |
| Bitwise-op        |  &nbsp; | template< class IntegerType ><br>constexpr byte & operator&#166;( byte l, byte r ) noexcept | bitwise-or-ed byte |
| &nbsp;            |  &nbsp; | template< class IntegerType ><br>constexpr byte & operator&amp;( byte l, byte r ) noexcept | bitwise-xor-ed byte |
| &nbsp;            |  &nbsp; | template< class IntegerType ><br>constexpr byte & operator&Hat;( byte l, byte r ) noexcept | bitwise-and-ed byte|
| Conversion        | non-std | template< class IntegerType ><br>constexpr byte to_byte( IntegerType v ) | byte with value v|
| &nbsp;            | >=C++11 | template< class IntegerType = *underlying-type* ><br>constexpr IntegerType to_integer( byte b ) | byte's value, note&nbsp;2,&nbsp;3|
| &nbsp;            | < C++11 | template< class IntegerType ><br>constexpr IntegerType to_integer( byte b ) | byte's value, note&nbsp;3 |

**Note 1**: the algrithms use an extra level of casting to prevent undefined behaviour, as mentioned by Thomas Köppe on mailing list isocpp-lib, subject "std::byte operations are hard to use correctly", on 16 March 2017.

**Note 2**: default template parameter as suggested by Zhihao Yuan on mailing list isocpp-lib, subject "std::byte to_integer<>", on 10 March 2017.

**Note 3**: use `to_integer()` to compute a byte's hash value.

### Configuration macros

Currently there are no configuration macros.


Reported to work with
---------------------
The table below mentions the compiler versions *byte lite* is reported to work with.

OS        | Compiler   | Versions |
---------:|:-----------|:---------|
Windows   | Clang/LLVM | ? |
&nbsp;    | GCC        | 5.2.0 |
&nbsp;    | Visual C++<br>(Visual Studio)| 6 (6), 8 (2005), 10 (2010), 11 (2012),<br>12 (2013), 14 (2015), 14 (2017) |
GNU/Linux | Clang/LLVM | 3.5.0 |
&nbsp;    | GCC        | 4.8.4 |
OS X      | ?          | ?   |


Building the tests
------------------
To build the tests you need:

- [CMake](http://cmake.org), version 2.8.12 or later to be installed and in your PATH.
- A [suitable compiler](#reported-to-work-with).

The [*lest* test framework](https://github.com/martinmoene/lest)  is included in the [test folder](test).

The following steps assume that the [*byte lite* source code](https://github.com/martinmoene/byte-lite) has been cloned into a directory named `c:\byte-lite`.

1. Create a directory for the build outputs for a particular architecture.
Here we use c:\byte-lite\build-win-x86-vc10.

        cd c:\byte-lite
        md build-win-x86-vc10
        cd build-win-x86-vc10

2. Configure CMake to use the compiler of your choice (run `cmake --help` for a list).

        cmake -G "Visual Studio 10 2010" ..

3. Build the test suite in the Debug configuration (alternatively use Release).

        cmake --build . --config Debug

4. Run the test suite.

        ctest -V -C Debug

All tests should pass, indicating your platform is supported and you are ready to use *byte lite*.


Other implementations of byte
----------------------------
- Martin Moene. [gsl lite](https://github.com/martinmoene/gsl-lite). C++98 and later.
- Microsoft. [Guideline Support Library (GSL)](https://github.com/microsoft/gsl). C++14 (supports MSVC 2013 and 2015).


Notes and References
--------------------
[1] CppReference. [byte](http://en.cppreference.com/w/cpp/types/byte).

[2] ISO/IEC WG21. [N4659, section 21.2.1, Header <cstddef> synopsis](http://wg21.link/n4659#page=492). March 2017.

[3] Neil MacIntosh. [P0298: A byte type definition (Revision 3)](http://wg21.link/p0298). March 2017.


Appendix
--------
### A.1 byte lite test specification

```
byte: Allows to construct from integral via static cast (C++17)
byte: Allows to construct from integral via byte() (C++17)
byte: Allows to construct from integral via to_byte()
byte: Allows to convert to integral via to_integer()
byte: Allows to convert to integral via to_integer(), using default type
byte: Allows comparison operations
byte: Allows bitwise or operation
byte: Allows bitwise and operation
byte: Allows bitwise x-or operation
byte: Allows bitwise or assignment
byte: Allows bitwise and assignment
byte: Allows bitwise x-or assignment
byte: Allows shift-left operation
byte: Allows shift-right operation
byte: Allows shift-left assignment
byte: Allows shift-right assignment
byte: Provides constexpr non-assignment operations (C++11)
byte: Provides constexpr assignment operations (C++14)
```
