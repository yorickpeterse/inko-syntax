# inko-syntax

A simple syntax highlighting library for [Inko](https://inko-lang.org), with
[Pygments](https://pygments.org/) compatible HTML output.

## Requirements

- Inko 0.13.1 or newer

## Installation

```bash
inko pkg add github.com/yorickpeterse/inko-syntax 0.1.0
inko pkg sync
```

## Supported languages

| Language                       | Identifier | Lexer
|:-------------------------------|:-----------|:-------------------------------
| [Inko](https://inko-lang.org/) | `inko`     | `syntax.lexers.inko.Lexer`

## Supported formats

| Format                   | Formatter
|:-------------------------|:---------------------------------
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
    let lang = langs.for_name('inko').unwrap

    # Now we can create a lexer and format it. Lexers take an immutable
    # reference to a `ByteArray`, so we need to keep it around until we've
    # produced the token stream.
    let bytes = '# This is a test'.to_byte_array
    let lexer = lang.lexer(bytes)

    # The `Html` formatter takes a lexer and turns it into Pygments compatible
    # HTML.
    let html = Html.new.format(lexer)

    STDOUT.new.print(html)
  }
}
```

The output of this is the following HTML:

```html
<div class="highlight"><pre class="highlight"><code><span class="c"># This is a test</span></code></pre></div>
```

Here's a more complicated example, showcasing a program that highlights a file
based on its extension:

```inko
import std.env.(arguments)
import std.fs.file.ReadOnlyFile
import std.stdio.STDOUT
import syntax.Languages
import syntax.format.Html

class async Main {
  fn async main {
    let out = STDOUT.new
    let path =
      arguments.opt(0).expect('a file must be specified as the first argument')

    let bytes = ByteArray.new

    match ReadOnlyFile.new(path).then(fn (f) { f.read_all(bytes) }) {
      case Ok(_) -> {}
      case Error(err) -> panic("failed to read {path}: {err}")
    }

    let tail = path.to_path.tail

    # This determines the language from the file's extension. This requires a
    # bit more work as Inko's `Path` type lacks a method to get the file
    # extension (see https://github.com/inko-lang/inko/issues/621 for more
    # details).
    let ext = tail.byte_index(of: '.', starting_at: 0)
      .map fn (i) { tail.slice(start: i + 1, size: tail.size).into_string }
      .unwrap_or('')

    let langs = Languages.new
    let lexer = langs
      .for_extension(ext)
      .expect("the extension '{ext}' isn't recognized")
      .lexer(bytes)

    out.print(Html.new.format(lexer))
  }
}
```

## License

All source code in this repository is licensed under the Mozilla Public License
version 2.0, unless stated otherwise. A copy of this license can be found in the
file "LICENSE".
