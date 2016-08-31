# linter-spell

[![Travis&#x2011;CI Build Status](http://img.shields.io/travis/yitzchak/linter-spell/master.svg?label=Linux/OSX%20build)](http://travis-ci.org/yitzchak/linter-spell) [![AppVeyor Build Status](http://img.shields.io/appveyor/ci/yitzchak/linter-spell/master.svg?label=Windows%20build)](http://ci.appveyor.com/project/yitzchak/linter-spell) [![David](http://img.shields.io/david/yitzchak/linter-spell.svg)](http://david-dm.org/yitzchak/linter-spell)

Multilingual grammar-specific spell checking for [Atom](http://atom.io) and
[linter](http://atom.io/packages/linter) using Ispell compatible interface such
as [GNU Aspell](http://aspell.net/) or [Hunspell](http://hunspell.github.io/).

## Installing

Use the Atom package manager and search for "linter-spell", or from a shell run

```bash
apm install linter-spell
```

## Prerequisites

This package relies on a Ispell compatible package such as
[GNU Aspell](http://aspell.net/) or [Hunspell](http://hunspell.github.io/).

## Usage

Spell checking is done upon document save with misspellings displayed by
[linter](http://atom.io/packages/linter). Misspellings can be corrected or added
to personal dictionary using the
[intentions](http://atom.io/packages/intentions) package via
<kbd>ctrl</kbd>+<kbd>enter</kbd> on OSX and <kbd>alt</kbd>+<kbd>enter</kbd> on
Linux and Windows.

Manual language selection can be done by clicking on the status bar language
indicator or by using the default keybinding <kbd>alt</kbd>+<kbd>y</kbd> on
Linux or <kbd>ctrl</kbd>+<kbd>shift</kbd>+<kbd>y</kbd> on other platforms.

## Grammar and Dictionary Providers

Spell checking plain text, Markdown, or AsciiDoc documents is included in the
package. To spell check other document types use a `linter-spell-grammar`
provider. To use additional dictionaries use `linter-spell-dictionary` provider.
Current providers are listed in the table below.

| Purpose                                                  | Package                                                                                     | Type               | Dependencies                                                                                                                                 | File Specific Language              |
|----------------------------------------------------------|---------------------------------------------------------------------------------------------|--------------------|----------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------|
| Ispell compatible spell checking and word breaking       | Included in linter&#x2011;spell                                                             | Dictionary         | None                                                                                                                                         | N/A                                 |
| Project specific dictionaries                            | [linter&#x2011;spell&#x2011;project](http://atom.io/packages/linter-spell-project)          | Dictionary         | None                                                                                                                                         | N/A                                 |
| AsciiDoc spell checking and dictionaries                 | Included in linter&#x2011;spell                                                             | Dictionary/Grammar | [language&#x2011;asciidoc](http://atom.io/packages/language-asciidoc)                                                                        | `:lang:` attribute                  |
| Git Commit Message spell checking and dictionaries       | Included in linter&#x2011;spell                                                             | Dictionary/Grammar | [language&#x2011;git](http://atom.io/packages/language-git)                                                                                  | None                                |
| GitHub flavored Markdown spell checking and dictionaries | Included in linter&#x2011;spell                                                             | Dictionary/Grammar | [language&#x2011;gfm](http://atom.io/packages/language-gfm)                                                                                  | None                                |
| HTML spell checking and dictionaries                     | [linter&#x2011;spell&#x2011;html](http://atom.io/packages/linter-spell-html)                | Dictionary/Grammar | [language&#x2011;html](http://atom.io/packages/language-html)                                                                                | `lang` attribute                    |
| Javascript spell checking and dictionaries               | [linter&#x2011;spell&#x2011;javascript](http://atom.io/packages/linter-spell-javascript)    | Dictionary/Grammar | [language&#x2011;javascript](http://atom.io/packages/language-javascript) or [language&#x2011;babel](http://atom.io/packages/language-babel) | None                                |
| JSON spell checking and dictionaries                     | [linter&#x2011;spell&#x2011;javascript](http://atom.io/packages/linter-spell-javascript)    | Dictionary/Grammar | [language&#x2011;json](http://atom.io/packages/language-json)                                                                                | None                                |
| LaTeX, TeX &amp; BibTeX spell checking and dictionaries  | [linter&#x2011;spell&#x2011;latex](http://atom.io/packages/linter-spell-latex)              | Dictionary/Grammar | [language&#x2011;tex](http://atom.io/packages/language-tex) (preferred) or [language&#x2011;latex](http://atom.io/packages/language-latex)   | `%!TeX spellcheck` magic comment    |
| LaTeX spell checking                                     | [linter&#x2011;spell&#x2011;latexsimple](https://atom.io/packages/linter-spell-latexsimple) | Grammar            | [language&#x2011;latexsimple](https://atom.io/packages/language-latexsimple)                                                                 | None                                |
| Markdown spell checking and dictionaries                 | Included in linter&#x2011;spell                                                             | Dictionary/Grammar | [language&#x2011;markdown](http://atom.io/packages/language-markdown)                                                                        | None                                |
| Plain Text spell checking and dictionaries               | Included in linter&#x2011;spell                                                             | Dictionary/Grammar | [language&#x2011;text](http://atom.io/packages/language-text)                                                                                | None                                |
| Ruby spell checking and dictionaries                     | [linter&#x2011;spell&#x2011;ruby](http://atom.io/packages/linter-spell-ruby)                | Dictionary/Grammar | [language&#x2011;ruby](http://atom.io/packages/language-ruby)                                                                                | None                                |

## Creating New Grammar Providers

New grammars can be added by implementing a `linter-spell-grammar` provider.
This can be done by adding the following to `package.json`

```json
"providedServices": {
  "linter-spell-grammar": {
    "versions": {
      "1.0.0": "provideGrammar"
    }
  }
}
```

The provided service should be as follows

```javascript
function provideGrammar () {
  return [{
    grammarScopes: ['source.gfm'],
    findLanguageTags: textEditor => { return ['en-US'] },
    checkedScopes: {
      'source.gfm': true,
      'markup.code.c.gfm': false,
      'markup.underline.link.gfm': () => atom.config.get('linter-spell.checkLinks')
    },
    filterRanges: (textEditor, ranges) => {
      return {
        ranges: ranges,
        ignoredRanges: []
      }
    }
  }]
}
```

Multiple grammars can be provided by returning an array. `grammarScopes` is
required, but all other properties and methods are optional.

The `findLanguageTags` method should scan `textEditor` for a file specific
override of the user's default language and return [RFC
5646](http://www.rfc-editor.org/rfc/rfc5646.txt) compliant language codes or `[]`
if no language references were found. See
[linter&#x2011;spell&#x2011;latex](http://atom.io/packages/linter-spell-latex)
for an implementation using TeX magic comments.

The `checkedScopes` list the grammar scopes that either checked or ignored. To
explicitly check a scope use a value of `true`, while `false` will ignore that
scope. To allow dynamic determination of scope spell checking a function may
also be supplied. The function should take no arguments and return a truthy
value. If this property is not provided then all scopes in `grammarScopes` will
be checked. When it is provided all scopes default to ignored unless specified
with a `true` value.

The `filterRanges` method should check the `ranges` parameter for sub-ranges
within each ranges which are valid to spell check. It should return a list
of modified ranges in `ranges` and can also return `ignoredRanges` for
ranges that should not be checked. The interval difference between `ranges`
and `ignoredRanges` will actually be checked.

## Create New Dictionary Providers

New dictionaries can be added by implementing a `linter-spell-dictionary`
provider. This can be done by adding the following to `package.json`

```json
"providedServices": {
  "linter-spell-dictionary": {
    "versions": {
      "1.0.0": "provideDictionary"
    }
  }
}
```

The provided service should be as follows

```javascript
function provideDictionary () {
  return [{
    name: 'Your dictionary name',
    grammarScopes: ['source.gfm'],
    languages: ['en-US'],
    checkRange: (textEditor, languages, range) => {
      return new Promise((resolve, reject) => resolve([{
        range: new Range([0, 1], [0, 4]),
        suggestions: ['bar'],
        actions: [{
          title: 'Add to my dictionary',
          apply: () => { /* add word to your dictionary. */ }
        }]
      }])
    },
    checkWord: (textEditor, languages, range) => {
      return new Promise((resolve, reject) => resolve({
        isWord: false, // return true if word is found
        suggestions: ['foo'],
        actions: [{
          title: 'Add to my dictionary',
          apply: () => { /* add word to your dictionary. */ }
        }]
      })
    }
  }]
}
```

Multiple grammars can be provided by returning an array. `name` and `checkWord`
are required, but all other properties and methods are optional. If
`grammarScopes` or `languages` are not provided then provider will be used for
all grammar scopes or languages, respectively.

If `checkRange` is provided then it is assumed that the dictionary knows how
find line breaks for all of the languages listed in `languages`. `linter-spell`
will then move that provider to the front of the queue and allow it to perform
word and breaks and return words that are potentially misspelled. Those
potential misspellings are then passed to the dictionary providers that only
define `checkWord` for an opportunity to check the word against their
dictionary.

If a provider cannot find a word in its dictionary then it should at least
return `{ isWord: false }`. It may also return a list of `suggestions` or a list
of `actions` to take on the misspelled word. For example, adding the word to a
language specific dictionary.
