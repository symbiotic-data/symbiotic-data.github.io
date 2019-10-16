# Home

[Symbiotic-Data](https://github.com/symbiotic-data) is an initiative intended on standardizing
primitive data types and their serialization formats, on different platforms and programming languages.

Different platforms have different capabilities, preconceptions, and opinions on how data should
be formatted - this project aims to clean some of that up, and formalize the data, so there's
less corrections needing to be made by you, the programmer.

Most implementations of each Symbiotic data type for each platform should be as painless and quick
as possible - most of the time being a no-op. But for some instances, it is necessary to restrict the
platforms natural understanding of a data type, in order to meet an upper-bound of its intended
target for communication. Likewise, serialization targets should be as simple as possible, but also
rigorously defined.

----------------

The backbone of insurance that these types are indeed correctly serialized and interpreted across-the-board,
is from the [Symbiote Test Suite](/testsuite), a protocol specifying how randomly generated data and operations on
that data should be communicated to a different party, implementing the same data type.

## Supported Platforms

Currently the supported platforms are:

- Haskell
  - [symbiote test suite](https://hackage.haskell.org/package/symbiote)
- PureScript
  - [symbiote test suite](https://pursuit.purescript.org/package/purescript-symbiote)

There are plans for implementations for:

- JavaScript (by use of the PureScript implementation)
- Rust
- Java / Kotlin
- Objective-C / Swift
- C# / F#
- C / C++
- PHP
- Ruby
- Python
- Go
- D

If you have any other requested implementations or contributions, feel free to open an issue or pull request!
