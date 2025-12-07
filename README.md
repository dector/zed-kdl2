# Zed KDL Support

Simple extension that provides syntax highlighting and language support
for the KDL language, used for configuration in projects such as the [niri](https://github.com/YaLTeR/niri) compositor.

## Features

- **KDLv2 Specification Support** - Full support for KDL 2.0 syntax
- **GLSL Syntax Highlighting** - Embedded GLSL code highlighting in `custom-shader` fields
- **Complete Editor Integration** - Bracket matching, code folding, indentation, and symbol navigation

## KDLv2 Support

This extension supports the KDL 2.0 specification with the following features:

### Tagged Literals
```kdl
node #true #false #null
```

### Raw Strings
```kdl
raw #"This is a raw string with "quotes""#
```

### Multi-line Strings
```kdl
description """
    This is a
    multi-line string
    """
```

### Migration from KDL v1

**Breaking changes in KDLv2:**
- Booleans and null now require `#` prefix: `#true`, `#false`, `#null`
- Raw strings use `#""#` syntax instead of `r""`
- Multi-line strings use `"""` triple quotes
- New escape sequence: `\s` for space
- Removed escape: `/` no longer needs escaping

Based on the [tree-sitter-kdl2](https://github.com/dector/tree-sitter-kdl2) grammar with KDLv2 support.
