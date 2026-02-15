# PHP Static Type Checker

This is a fast and simple static code analyzer for PHP. It depends on the `php-ast` PECL extension.
It resolves included files automatically and uses reflection to support any PECL extensions without
requiring stubs. The author can be contacted by e-mail at `jumping-beaver@mailbox.org`.

## How to install and use

```
wget https://codeberg.org/Jumping-Beaver/php_static_type_checker/raw/branch/master/php_static_type_checker.php
chmod +x php_static_type_checker.php
./php_static_type_checker.php
```

## Design objectives

- Released in a single PHP file with no dependencies besides `php-ast`.

- The key objective is developer efficiency, particularly to waste less time on fixing typos.
  Reducing the amount of defects that reach production is a side effect. Performance and ease of
  use are more important than finding as many errors as possible.

- Not using a configuration file because the tool shall be simple to use.

- False positives are not desirable.

- This application omits non-essential features in favor of performance and maintainability.
  Out of scope are:
   - extensive support for PHPDoc comments,
   - support for outdated PHP versions,
   - suggestions where to add more type hints,
   - detection of dead code and unused identifiers are of scope, since it creates no runtime
     defects and may obstruct debugging,
   - sophisticated analysis of control structures,
   - to validate assignments of constants and default values, because they may contain arithmetic
     expressions, which are too hard to evaluate.

## How could PHP better support static code analysis?

This tool constructs reflection types from the AST, which took considerable effort and is a
duplication of what PHP does internally. One can think of possible approaches to improve this.

Static code analysis would be more powerful if we had more descriptive array and list data
types. Other static code analyzers rely on doc comments here, another solution would be attributes.

Intersection types have only rare and not important use cases but introduce a lot of complexity. I
think it was a mistake to introduce this feature.

Case insensitive identifiers in PHP are a big bummer. It would be nice to have this removed in
future versions.

PHP has different data schemas for union type hints, type hints with a single type, and nullable
types, requiring lots of case distinctions. A type hint with a single type could be stored as a
list with one entry, making it a special case of union types. Instead, we have the inconsistent
interfaces `ReflectionNamedType` and `ReflectionUnionType`.

What I don't like is that a parameter type hint `function (string $parameter=null) {...}` is
treated by PHP just like `function (?string $parameter=null) {...}`. This is surprising behavior.
This magical transformation of the type hint only works for `null` default values and only for
function arguments, not for properties.

The `insteadof` operator is awkward. Something like `ignore trait2::method` would be clearer, more
powerful, and stricter (requiring `trait2::method` to exist) than `trait1::method insteadof trait2` 

An inconsistency: a type flag exists for `static` but not for `self` and `parent`.
