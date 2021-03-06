# Configuration Format

The floskell configuration file is a `JSON` file.  The basic structure
is as follows:

```json
{
  "language": "Haskell2010",
  "extensions": [],
  "style": "base",
  "formatting": {
    "penalty": {...},
    "layout": {...},
    "indent": {...},
    "align": {...},
    "group": {...},
    "op": {...},
    "options": {...}
    }
  }
}
```

## Parser Settings

* `language` (`Haskell2010`): The Haskell language standard for parsing source files.
  Either `Haskell98` or `Haskell2010`.

  ```json
  {
    "language": "Haskell2010"
  }
  ```

* `extensions` (`[]`): A list of extensions to be enabled or disabled.  Check
  the output of `floskell --help` for the full list of extensions.  To
  disable an extension, prefix the extension with `No`.

  ```json
  {
    "extensions": ["RecordWildCards", "NoTypeFamilies"]
  }
  ```

* `fixities` (`[]`): A list of additional infix declarations to use
  while parsing.  Check the output of `floskell --help` for a list of
  built-in declarations.

  ```json
  {
    "fixities": ["infixr 9 ."]
  }
  ```

## Style Definition

* `style` (`base`): The formatting style to use.  The style can be fine-tuned
  using the `formatting` configuration element.  Check the output of
  `floskell --help` for the full list of styles.

* `formatting` (`{}`): Formatting settings, applied on top of the selected
  style.

### Line Penalty

* `penalty`: Defines the parameters for the penalty function used to
  judge the overall layout.

  * `indent` (`1`): Penalty for each indentation character.
  * `linebreak` (`10`): Penalty for each line.
  * `max-line-length` (`80`): Character limit for each line.
  * `overfull` (`10`): Penalty for each character over the line limit.
  * `overfull-once` (`200`): Penalty for each overfull line.

### Layout Rules

* `layout`: Defines the accepted layout choices for all configurable
  language constructs.

  Allowed values are `flex`, `vertical`, or `try-oneline`.

  * `app` (`flex`): Function application
  * `con-decls` (`flex`): Data constructor declarations
  * `declaration` (`flex`): Declarations
  * `export-spec-list` (`flex`): Module export list
  * `if` (`flex`): If-then-else
  * `import-spec-list` (`flex`): Module import lists
  * `infix-app` (`flex`): Infix operator application
  * `let` (`flex`): Let-in
  * `list-comp` (`flex`): List comprehensions
  * `record` (`flex`): Record declaration, construction, and update
  * `typesig` (`flex`): Type signatures

### Indentation Rules

* `indent`: Defines the indentation choices for all configurable
  language constructs.

  Allowed values (except for `onside`, `deriving`, `where`) are
  `align`, `indent-by n`, and `align-or-indent-by n`.

  Allowed values for `onside` are all integers greater than zero.

  Allowed values for `deriving` and `where` are all integers greater
  than or equal to zero.

  * `onside` (`4`): Onside indentation
  * `deriving` (`4`): Indentation for deriving lists
  * `where` (`2`): Indentation for where keyword after functions
  * `case` (`indent-by 4`): Case analysis
  * `class` (`indent-by 4`): Class and instance declarations
  * `do` (`indent-by 4`): Do and recursive do blocks
  * `export-spec-list` (`indent-by 4`): Module export list
  * `if` (`indent-by 4`): If-then-else
  * `import-spec-list` (`indent-by 4`): Module import lists
  * `let` (`indent-by 4`): Let bindings
  * `let-binds` (`indent-by 4`): Let bindings
  * `let-in` (`indent-by 4`): Expression in let
  * `multi-if` (`indent-by 4`): Guards and multi-way if
  * `where-binds` (`indent-by 2`): Bindings in where block after functions

### Tabstop Alignment

* `align`: Defines the enabled tabstop alignments and their limitations.

  Allowed values, except for `limits`, are `true` and `false`.

  The item `limits` must be a list with two integers, the first
  integer is a number of character (`absolute limit`), the second a
  percentage (`relative limit`).  Alignment will be disabled if the
  length of any inserted padding is larger than `absolute limit` and
  is larger than `relative limit` of the maximum field length (tabstop
  column minus indentation).

  * `limits` (`[10, 25]`): Alignment limits
  * `case` (`false`): Case patterns
  * `class` (`false`): Declarations inside class and instance declarations
  * `import-module` (`false`): Names of imported modules
  * `import-spec` (`false`): Module import lists
  * `let-binds` (`false`): Let bindings
  * `record-fields` (`false`): Record field assignments and type signatures
  * `where` (`false`): Declarations in where bindings for functions

### Whitespace Rules

* `group`: Defines whitespace rules for parentheses, brackets,
  braces, and other enclosing punctuation characters.

  The `group` item is a map from enclosing punctuation (identified
  by the opening characters) and optional context to a whitespace
  configuration. The key can be either

  * `default`: Defines the fallback whitespace configuration,

  * `* in <context>`: Defines the default whitespace configuration
    within the given context,

  * `<punct> in *`: Defines the default whitespace configuration for
    the given punctuation, or

  * `<punct> in <context>`: Defines the whitespace configuration for
    the given punctuation within the given context.

  The value is an object with the following fields:

  * `spaces`: Where to insert spaces.
  * `linebreaks`: Where to insert linebreaks.
  * `force-linebreak`: Whether to enforce vertical layout.

  An example:

  ```haskell
  {
    "group": {
      "default": {
        "spaces": "none",
        "linebreaks": "after",
        "force-linebreak": false
      },
      "(": {
        "spaces": "both",
        "linebreaks": "after",
        "force-linebreak": false
      },
      "( in pattern": {
        "spaces": "none",
        "linebreaks": "none",
        "force-linebreak": false
      }
    }
  }
  ```

* `op`: Defines whitespacce rules for infix operators.

  See `group` above.

### Other Style Options

* `options`: Defines additional formatting rules for module heads.

  * `align-sum-type-decl` (`false`): Whether to align `=` with `|` in the declaration of sum types.
  * `preserve-vertical-space` (`false`): Whether to preserve additional vertical space between declarations, statements, and a few other places.
  * `sort-import-lists` (`false`): Whether to sort import statements by the name of the imported module.
  * `sort-imports` (`false`): Whether to sort import lists.
  * `sort-pragmas` (`false`): Whether to sort module pragmas (`LANGUAGE`, `OPTION`, and `ANN`)
  * `split-language-pragmas` (`false`): Whether to split `LANGUAGE` pragmas.

### Sorting imports configuration

There are 3 main import sorting modes:

  * don't sort: `sort-imports: false`
  * sort and group according to first component in module path: Data.Maybe goes with
    Data.Either, Control.Monad goes with Control.Applicative: `sort-imports: true`
  * sort and group according to user configuration: `sort-imports` takes a list of groups
    of prefixes: `[["Data", "Control.Monad"], ["Control"], [], "Test"]`.
    For this configuration imports are arranged in 4 groups:
    * `Control.Monad` followed by anything under `Control.Monad.*` hierarchy, `Data` and
       anything under `Data.*`
    * `Control` (if you have it) and anything under `Control.*`
    * Anything not captured into previous or subsequent groups. If `[]` is not used
      unmatched imports will be appended at the end
    * `Test` and anything under `Test.*`
