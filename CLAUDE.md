# Zed KDL Support Extension

## Project Overview

This is a Zed editor extension that provides comprehensive language support for KDL (KDL Document Language). KDL is a configuration language used in projects like the Niri compositor. The extension includes specialized functionality to highlight embedded GLSL code blocks within KDL files.

**Extension Details:**
- **Extension ID:** kdl
- **Version:** 0.0.1
- **Author:** ElKowar (dev@elkowar.dev)
- **Repository:** https://github.com/elkowar/zed-kdl

## Project Structure

```
zed-kdl2/
├── extension.toml           # Extension manifest
├── README.md               # Project documentation
├── .gitignore              # Git ignore rules
└── languages/
    └── kdl/
        ├── config.toml     # Language configuration
        ├── highlights.scm  # Syntax highlighting rules
        ├── brackets.scm    # Bracket matching rules
        ├── folds.scm       # Code folding rules
        ├── indents.scm     # Indentation rules
        ├── injections.scm  # Language injection (GLSL support)
        ├── locals.scm      # Scope and reference rules
        └── outline.scm     # Symbol outline/navigation
```

## Technologies

- **Tree Sitter Query Language (*.scm):** All language definition files
- **TOML:** Configuration files
- **Markdown:** Documentation

**Dependencies:**
- tree-sitter-kdl grammar (GitHub: tree-sitter-grammars/tree-sitter-kdl)
- Commit: b37e3d58e5c5cf8d739b315d6114e02d42e66664

## Core Components

### Extension Configuration

#### extension.toml:11
Extension manifest that declares:
- Extension metadata (id, name, version, author, description)
- KDL language registration
- Tree-sitter grammar reference with specific commit hash
- Schema version (1)

#### languages/kdl/config.toml:5
Language configuration:
- Language name: "Kdl"
- Grammar: "kdl"
- File extensions: `.kdl`
- Line comment syntax: `//`

### Language Features

#### languages/kdl/highlights.scm:46
Syntax highlighting patterns for:
- **Types:** Node identifiers and type annotations
- **Variables:** Identifiers and property names
- **Operators:** `=`, `+`, `-`
- **Literals:**
  - Strings with escape sequences
  - Numbers (decimal and exponent forms)
  - Booleans (true/false)
  - Null values
- **Punctuation:** Brackets `{}`, parentheses `()`, semicolons `;`
- **Comments:** Single-line and multi-line with priority rendering

#### languages/kdl/brackets.scm:2
Bracket matching for:
- Curly braces `{}`
- Parentheses `()`

#### languages/kdl/folds.scm:8
Code folding support for:
- Node blocks
- Node children blocks
- String literals
- Multi-line comments

#### languages/kdl/indents.scm:7
Smart indentation rules based on:
- `node_children` blocks
- Nested braces

#### languages/kdl/injections.scm:8
**Special Feature:** Automatic GLSL syntax highlighting
- Recognizes GLSL code in fields named `custom-shader`
- Common use case for Niri compositor configuration

#### languages/kdl/locals.scm:10
Scope and reference definitions:
- Document-level scope
- Nested scope definitions
- Symbol reference resolution

#### languages/kdl/outline.scm:2
Symbol navigation support:
- Outline for nodes
- Context-aware breadcrumbs

## Key Features

1. **Comprehensive Syntax Highlighting**
   - Full KDL token support
   - Proper handling of strings, numbers, and special characters
   - Comment priority rendering

2. **Smart Editor Features**
   - Bracket/parenthesis matching
   - Code folding for nodes, children, and comments
   - Context-aware indentation
   - Symbol outline for navigation

3. **GLSL Integration**
   - Automatic language injection for `custom-shader` fields
   - Enables GLSL syntax highlighting within KDL files
   - Particularly useful for Niri compositor configuration

4. **Scope Awareness**
   - Proper scope definitions for IDE features
   - Reference resolution support

## Development Notes

### Recent Changes
- **e2f7ab7** (Aug 28, 2024): Rename to kdl
- **2317afe** (Aug 24, 2024): Basic support for KDL files - initial implementation

### Project Stats
- **Total Source Lines:** ~102 lines (excluding README)
- **Configuration:** Minimal, focused setup
- **Dependencies:** Single tree-sitter grammar dependency

## Use Cases

**Primary:** Configuration file editing for projects using KDL
- Niri compositor configuration
- Other KDL-based configuration files

**Secondary:** GLSL shader code editing within KDL custom-shader fields

## Architecture Patterns

This extension follows standard Zed extension patterns:

1. **Modular Language Definition**
   - Separate `.scm` files for different concerns
   - Clean separation between highlighting, folding, indentation, etc.

2. **Tree-sitter Integration**
   - Leverages tree-sitter-kdl grammar
   - Uses tree-sitter query language for all patterns

3. **Language Injection**
   - Demonstrates advanced feature: embedding GLSL syntax
   - Conditional injection based on field names

## Contributing

When modifying this extension:

1. **Syntax Highlighting** (highlights.scm)
   - Add new token patterns using tree-sitter query syntax
   - Follow existing capture name conventions (@type, @variable, etc.)
   - Test with various KDL files

2. **Editor Features**
   - Brackets: Update brackets.scm for new matching pairs
   - Folding: Add fold points in folds.scm
   - Indentation: Modify indents.scm for new indent rules

3. **Language Injection**
   - Extend injections.scm for additional embedded languages
   - Use field name matching pattern for conditional injection

4. **Testing**
   - Test with real KDL files (e.g., Niri configuration)
   - Verify GLSL highlighting in custom-shader fields
   - Check bracket matching and code folding

## Grammar Reference

The tree-sitter-kdl grammar provides the parsing foundation. Key node types:
- `node`: Top-level KDL nodes
- `node_children`: Nested children blocks
- `identifier`, `prop_identifier`: Various identifier types
- `type`: Type annotations
- String, number, boolean, null literals
- Comments (single-line and multi-line)

For grammar details, see: https://github.com/tree-sitter-grammars/tree-sitter-kdl
