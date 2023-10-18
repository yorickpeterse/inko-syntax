# inko-syntax

A simple syntax highlighting library for [Inko](https://inko-lang.org), with
[Pygments](https://pygments.org/) compatible HTML output.

# Requirements

- Inko 0.13.1 or newer

# Installation

```bash
inko pkg add github.com/yorickpeterse/inko-syntax 0.1.0
inko pkg sync
```

# Supported languages

| Language                       | Identifier | Lexer
|:-------------------------------|:-----------|:-------------------------------
| [Inko](https://inko-lang.org/) | `inko`     | `syntax.lexers.inko.Lexer`

# Supported formats

| Format                   | Formatter
|:-------------------------|:---------------------------------
| Pygments compatible HTML | `syntax.format.Html`

# Usage

A basic example using the default settings:

```inko
import std.stdio.STDOUT
import syntax.Syntax

class async Main {
  fn async main {
    # Syntax.new returns a None if the language isn't recognized. Here we use
    # unwrap() for the sake of simplicity, but in a real application you'll want to
    # print a custom error message of some sort instead.
    let syntax = Syntax.new('inko').unwrap

    # The Syntax.highlight() method takes a `ByteArray` and syntax highlights it
    # into a `String`. The default output format is HTML.
    let html = syntax.highlight('# This is a test'.to_byte_array)

    STDOUT.new.print(html)
  }
}
```

Using a custom format:

```inko
import std.stdio.STDOUT
import syntax.Syntax
import syntax.format.(Format, Html)

class async Main {
  fn async main {
    let syntax = Syntax.new('inko').unwrap
    let format = Html.new

    format.class = 'syntax-highlight'
    syntax.format = format as Format

    let html = syntax.highlight('# This is a test'.to_byte_array)

    STDOUT.new.print(html)
  }
}
```

# License

All source code in this repository is licensed under the Mozilla Public License
version 2.0, unless stated otherwise. A copy of this license can be found in the
file "LICENSE".
