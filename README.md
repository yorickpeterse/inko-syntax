# inko-syntax

A simple syntax highlighting library for [Inko](https://inko-lang.org), with
[Pygments](https://pygments.org/) compatible HTML output.

This library focuses solely on syntax highlighting, and doesn't provide any
means to detect a language/lexer to use according to a file extension, filename,
MIME type, or the file contents.

## Requirements

- Inko 0.13.2 or newer

## Installation

```bash
inko pkg add github.com/yorickpeterse/inko-syntax 0.3.0
inko pkg sync
```

## Supported languages

| Language                                      | Name      | Aliases
|:----------------------------------------------|:----------|:-------
| [C](https://www.open-std.org/jtc1/sc22/wg14/) | `c`       | `cpp`
| [Fish](https://fishshell.com/)                | `fish`    |
| [Inko](https://inko-lang.org/)                | `inko`    |
| [Make](https://www.gnu.org/software/make/)    | `make`    |
| [Ruby](https://www.ruby-lang.org/en/)         | `ruby`    |
| [Rust](https://www.rust-lang.org/)            | `rust`    |
| Shell                                         | `shell`   | `bash`, `sh`, `zsh`, `ksh`
| [TOML](https://toml.io/)                      | `toml`    |

## Supported formats

| Format                   | Formatter
|:-------------------------|:--------------------
| Pygments compatible HTML | `syntax.format.Html`

## Usage

A basic example:

```inko
import std.stdio.STDOUT
import syntax.Languages
import syntax.format.Html

class async Main {
  fn async main {
    # The `Languages` type is a registry of the available languages. This type
    # makes it easy to create a lexer for a language, without having to
    # explicitly import the underlying types into your code.
    let langs = Languages.new

    # This gets a language for the given name, returning a `None` if the name
    # isn't recognized.
    let lang = langs.get('inko').unwrap

    # Now we can create a lexer and format it. Lexers take an immutable
    # reference to a `ByteArray`, so we need to keep it around until we've
    # produced the token stream.
    let bytes = '# This is a test'.to_byte_array
    let lexer = lang.lexer(bytes)

    # The `Html` formatter takes a lexer and turns it into Pygments compatible
    # HTML document fragment.
    let html = Html.new.format(lexer)

    STDOUT.new.print(html.to_string)
  }
}
```

The output of this is the following HTML (formatted manually to increase
readability):

```html
<div class="highlight">
  <pre class="highlight"><code><span class="c"># This is a test</span></code></pre>
</div>
```

## License

All source code in this repository is licensed under the Mozilla Public License
version 2.0, unless stated otherwise. A copy of this license can be found in the
file "LICENSE".
