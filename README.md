## js-codemod [![Build Status](https://travis-ci.org/cpojer/js-codemod.svg)](https://travis-ci.org/cpojer/js-codemod)

This repository contains a collection of codemod scripts for use with
[JSCodeshift](https://github.com/facebook/jscodeshift).

### Setup & Run

```sh
npm install -g jscodeshift
git clone https://github.com/cpojer/js-codemod.git
jscodeshift -t <codemod-script> <file>
```

Use the `-d` option for a dry-run and use `-p` to print the output for
comparison.

### Included Scripts

#### `arrow-function`

Transforms functions to arrow functions

```sh
jscodeshift -t js-codemod/transforms/arrow-function.js <file>
```

It will transform `function() { }.bind(this)` calls to `() => {}`. If the only
statement in the body is a `ReturnStatement` it will remove the curly braces.
If you are feeling lucky and you know that returning the value of
single-expression functions will not affect the behavior of your application you
can specify the `--inline-single-expressions=true` option and it will transform
`function() { relay(); }.bind(this)` to `() => relay()` instead of
`() => { relay(); }`.

#### `use-strict`

Adds a top-level `'use strict'` statement to JavaScript files

```sh
jscodeshift -t js-codemod/transforms/use-strict.js <file>
```

#### `object-shorthand`

Transforms object literals to use [ES6 shorthand](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Object_initializer#New_notations_in_ECMAScript_2015)
for properties and methods.

```sh
jscodeshift -t js-codemod/transforms/object-shorthand.js <file>
```

#### `rm-requires`

Removes any requires where the imported value is not referenced. Additionally
if any module is required more than once the two requires will be merged.

```sh
jscodeshift -t js-codemod/transforms/rm-requires.js <file>
```

#### `template-literals`

Replaces string concatenation with template literals.

```sh
jscodeshift -t js-codemod/transforms/template-literals.js <file>
```

Adapted from ["How to write a codemod" by Ramana Venkata](https://vramana.github.io/blog/2015/12/21/codemod-tutorial/).

Areas of improvement:

- Comments in the middle of string concatenation are currently added before the
  string but after the assignment. Perhaps in these situations, the string
  concatenation should be preserved as-is.

- Nested concatenation inside template literals is not currently simplified.
  Currently, a + `b${'c' + d}` becomes `${a}b${'c' + d}` but it would ideally
  become ``${a}b${`c${d}`}``.

- Unnecessary escaping of quotes from the resulting template literals is
  currently not removed. This is possibly the domain of a different transform.

- Unicode escape sequences are converted to unicode characters when the
  simplified concatenation results in a string literal instead of a template
  literal. It would be nice to perserve the original--whether it be a unicode
  escape sequence or a unicode character.

#### `trailing-commas`

Adds trailing commas to array and object literals.

```sh
jscodeshift -t js-codemod/transforms/trailing-commas.js <file>
```

#### `unquote-properties`

Removes quotes from object properties whose keys are strings which are valid
identifiers.

```sh
jscodeshift -t js-codemod/transforms/unquote-properties.js <file>
```

### Included extensions

`jscodeshift-imports` helpers for modifying `import` and `require` statements,
[see docs](extensions/imports/).

### Recast Options

Options to [recast](https://github.com/benjamn/recast)'s printer can be provided
through the `printOptions` command line argument

```sh
jscodeshift -t transform.js <file> --printOptions='{"quote":"double"}'
```
