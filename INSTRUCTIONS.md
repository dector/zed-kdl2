# KDLv2 Migration Instructions

## Current Status

The current tree-sitter-kdl grammar (at commit `b37e3d58e5c5cf8d739b315d6114e02d42e66664`) **does not support KDLv2**. It only supports KDL v1.0 syntax.

### Key KDLv2 Changes Not Yet Supported:

1. **Tagged literals**: `#true`, `#false`, `#null` instead of `true`, `false`, `null`
2. **Raw strings**: `#""#` syntax instead of `r""`
3. **Multi-line strings**: Triple-quote `"""` delimiters
4. **Version marker**: `/- kdl-version 2` support

### The Situation

The tree-sitter-grammars/tree-sitter-kdl grammar has **not been updated for KDLv2** yet. The latest release (v1.1.0 from May 2023) predates the KDL 2.0 specification finalization.

---

## Steps to Fork and Update

### 1. Fork the Repository

```bash
# Go to https://github.com/tree-sitter-grammars/tree-sitter-kdl
# Click "Fork" button to create your own fork
# Clone your fork locally
git clone https://github.com/YOUR_USERNAME/tree-sitter-kdl
cd tree-sitter-kdl
```

### 2. Use This Prompt for Claude

Copy and paste this entire prompt when working in the forked repository:

---

**PROMPT FOR CLAUDE:**

```
I need you to update this tree-sitter-kdl grammar to support KDLv2 specification. The grammar currently only supports KDL v1.0, but KDL 2.0 has been finalized with breaking changes.

## Required Changes

Based on the official KDLv2 specification (https://kdl.dev/spec/ and https://github.com/kdl-org/kdl/blob/main/SPEC.md), implement these changes:

### 1. Boolean and Null Values (BREAKING)
- Change `true`, `false`, `null` to require `#` prefix: `#true`, `#false`, `#null`
- The unprefixed versions should now be treated as regular identifiers, NOT special keywords
- Update grammar.js to recognize tagged literals with `#` prefix

### 2. Raw Strings (BREAKING)
- Old syntax: `r"raw string"` or `r#"raw"#`
- New syntax: `#"raw string"#` (no `r` prefix)
- Update the raw string parsing logic in both grammar.js and src/scanner.c

### 3. Multi-line Strings (NEW FEATURE)
- Add support for triple-quoted strings: `"""`
- Opening delimiter MUST be followed immediately by a newline
- Closing delimiter MUST be on its own line with optional whitespace prefix
- The common leading whitespace is stripped from all lines
- Example:
  ```kdl
  node """
       This is a
       multi-line string
       """
  ```

### 4. String Escape Changes
- Remove `/` (solidus/forward slash) from escaped characters
- Add `\s` escape for space character (U+0020)
- Update escape sequence patterns in grammar

### 5. Banned Characters (Security)
- Ban literal use of:
  - Code points under 0x20 (except newline and whitespace)
  - Code points above 0x10FFFF
  - Delete control character (0x7F)
  - Unicode direction control characters (U+202A through U+202E, U+2066 through U+2069)
- These can only appear via escapes in regular strings, not in raw strings

### 6. Line Continuations
- Allow line continuations followed by EOF (not just newline)
- Example: `node \<EOF>` is now valid

### 7. Optional Version Marker
- Support optional first line: `/- kdl-version 2` (or `1`)
- Can be preceded by BOM
- This helps parsers know which spec version to use

### 8. Code Point Constraints
- Restrict all code points to Unicode Scalar Values only
- Ensure all KDL documents are valid UTF-8
- Update string escape validation (e.g., `\u{}` sequences)

### 9. BOM Handling
- BOM (U+FEFF) only allowed as first character in document
- No longer treated as generic whitespace elsewhere

## Implementation Guidance

1. Start by reading the current grammar.js and understanding the structure
2. Update the lexical rules for keywords (true/false/null → tagged literals)
3. Modify the raw string scanner in src/scanner.c
4. Add multi-line string support as a new rule
5. Update escape sequence validation
6. Add character validation for banned code points
7. Update test cases in test/corpus/ to use KDLv2 syntax
8. Ensure backward incompatibility is intentional (v1 docs should fail to parse or parse identically)

## Testing

After implementing:
1. Run `npm test` or equivalent to verify all tests pass
2. Create test cases for each new KDLv2 feature
3. Verify that KDL v1 documents either fail or parse with identical semantics
4. Test edge cases: BOM handling, line continuations with EOF, multi-line strings

## Documentation

Update README.md to:
- Specify this grammar supports KDL 2.0 specification
- Document the breaking changes from v1
- Provide migration examples

Please implement these changes systematically, testing as you go. Let me know if you need clarification on any KDLv2 features.

Sources:
- KDL 2.0 Specification: https://kdl.dev/spec/
- KDL GitHub SPEC.md: https://github.com/kdl-org/kdl/blob/main/SPEC.md
```

---

### 3. After Grammar is Updated

Once Claude completes the grammar update in your fork:

```bash
# In your fork, after changes are committed
git add .
git commit -m "feat: add KDLv2 specification support"
git push origin main

# Get the latest commit hash
git rev-parse HEAD
```

Then return to this repository and provide:
1. Your fork's repository URL (e.g., `https://github.com/YOUR_USERNAME/tree-sitter-kdl`)
2. The commit hash from the updated grammar

---

## Next Steps (After Grammar Update)

Once you have the updated grammar, the following changes will be needed in this Zed extension:

### 4. Update extension.toml

Replace the grammar repository and commit hash:

```toml
[grammars.kdl]
repository = "https://github.com/YOUR_USERNAME/tree-sitter-kdl"
commit = "NEW_COMMIT_HASH"
```

### 5. Update highlights.scm

Add syntax highlighting for KDLv2 features:

```scheme
; Tagged literals (KDLv2)
[
  "#true"
  "#false"
] @boolean

"#null" @constant

; Raw strings (KDLv2 syntax: #""#)
(raw_string) @string

; Multi-line strings (KDLv2)
(multiline_string) @string
```

### 6. Update folds.scm

Add folding support for multi-line strings:

```scheme
[
  (node)
  (node_children)
  (string)
  (multiline_string)  ; Add this
  (multi_line_comment)
] @fold
```

### 7. Test the Extension

Create test KDLv2 files to verify:

```kdl
/- kdl-version 2

node-name #true #false #null

raw-example #"This is a raw string"#

multi-line """
    This is a
    multi-line string
    with proper indentation
    """

custom-shader """
    #version 330 core
    void main() {
        gl_FragColor = vec4(1.0);
    }
    """
```

---

## Summary

The tree-sitter-kdl grammar needs to be updated first before we can fully support KDLv2 in this Zed extension. The main grammar changes needed are:

- Tagged literals (`#true`, `#false`, `#null`)
- New raw string syntax (`#""#`)
- Multi-line strings with `"""`
- Updated escape sequences (`\s` for space, remove `/`)
- Security improvements (banned characters)

Once you have the updated grammar, we'll update this extension's highlighting and folding rules to match.

## Resources

- [KDL 2.0 Specification](https://kdl.dev/spec/)
- [KDL GitHub SPEC.md](https://github.com/kdl-org/kdl/blob/main/SPEC.md)
- [tree-sitter-kdl Grammar](https://github.com/tree-sitter-grammars/tree-sitter-kdl)
- [KDL Releases](https://github.com/kdl-org/kdl/releases)
- [Tree-sitter Grammar Writing Guide](https://tree-sitter.github.io/tree-sitter/creating-parsers)
