# ROADMAP

## ROADMAP — slug Tool

This document outlines the development roadmap for the **slug** command-line tool, including current functionality and planned improvements. It also includes implementation guidance for future enhancements.

The goal of this tool is to provide a **stable, multilingual slug generation utility** for documentation systems, static site generators, and metadata workflows.

## Adjustments

### Handling quotes in provided strings
 
For example both of these prompt give errors

```bash
slug What belongs in `trust_boundary.yml`
trust_boundary.yml: command not found
what-belongs-in
```
AND
```bash
slug "What belongs in `trust_boundary.yml`"
trust_boundary.yml: command not found
what-belongs-in
```
## Current Status — Version 1

Version 1 focuses on the principle:

**Make it Work**

The tool currently provides reliable slug generation with support for pipelines and configurable behavior.

## Implemented Features

- CLI command `slug`
- Accepts arguments or stdin
- Multilingual text handling
- YAML configuration file
- Uses `python-slugify`
- Runs inside an isolated Python virtual environment
- Works as a standard Unix-style command

Example usage:

```
slug "Référence API"
```

Output:

```
reference-api
```

Pipeline usage:

```
echo "Accessibilité numérique" | slug
```

Output:

```
accessibilite-numerique
```

---

# Project Structure

Example working layout:

```
~/bin/
├── slug
└── slugify-tool/
    ├── config.yml
    ├── slugify_cli.py
```

The wrapper command:

```
~/bin/slug
```

executes the Python CLI tool inside the virtual environment.

---

# Configuration System

The tool reads configuration from:

```
config.yml
```

Example configuration:

```yaml
lowercase: true
separator: "-"
ascii: true
max_length: 80
stopwords:
  - de
  - la
  - les
```

Configuration controls slug generation behavior.

## Version 1.1 Goals

A couple of these goals may be better served with other tools...

Phase two follows the principle:

**Make it Right**

The goal is to improve reliability, usability, and integration with documentation workflows.

Planned improvements which may require changes to this script:

- ingress: Automated Markdown title extraction
- egress : sidecar metadata filename generation
- improved configuration handling
- multilingual presets
- validation mode

#### Feature: Markdown Title Extraction

Allow slug generation directly from Markdown files.

Example command:

```
slug file.md
```

The tool should read the first H1 heading:

```
# Accessibilité numérique
```

Generated slug:

```
accessibilite-numerique
```

##### Implementation Plan

Add a function to detect H1 headings:

```python
def extract_markdown_title(path):
    with open(path, encoding="utf-8") as f:
        for line in f:
            if line.startswith("# "):
                return line[2:].strip()
    return None
```

CLI behavior:

- if argument is a file
- detect `.md`
- extract H1
- slugify title

#### Feature: Metadata Sidecar Filename Generation

The tool should assist metadata workflows.

Example:

```
slug --sidecar "Référence API"
```

Output:

```
.reference-api.metadata.yaml
```

This supports the canonical metadata sidecar architecture used in documentation systems.

##### Implementation Plan

Add CLI option:

```
--sidecar
```

Implementation:

```python
if args.sidecar:
    print(f".{slug}.metadata.yaml")
```

#### Feature: Multilingual Presets

Documentation systems frequently require different slug rules per language.

Example configuration:

```yaml
language: fr
```

The tool could automatically apply:

- French stopwords
- accent handling
- separator conventions

##### Implementation Strategy

Create language preset files:

```
presets/
    fr.yml
    en.yml
    es.yml
```

Example:

```yaml
stopwords:
  - de
  - la
  - les
  - des
```

Merge preset configuration with `config.yml`.

---

#### Feature: Slug Validation Mode

Documentation systems often require filenames to match titles.

Example:

```
slug --check file.md
```

Behavior:

- extract title
- compute slug
- compare with filename
- exit with status code if mismatch

Example output:

```
Filename mismatch:
expected: accessibilite-numerique.md
actual: article.md
```

Useful for CI pipelines.

#### Feature: Maximum Length Enforcement

Some systems require shorter slugs.

Example configuration:

```yaml
max_length: 60
```

Ensure truncation occurs at word boundaries.

---

#### Feature: CLI Improvements

Add CLI options:

```
slug --config file.yml
slug --sidecar
slug --check
slug --file
```

Example:

```
slug --sidecar file.md
```

---

### Version 2 Goals

Phase three follows:

**Make it Fast / Make it Powerful**

Potential improvements:

- packaging with pipx
- distribution via PyPI
- GUI configuration tool
- plugin architecture
- batch slug generation

#### Packaging Roadmap

Eventually the tool could become installable:

```
pipx install slug-tool
```

Repository layout:

```
slug/
├── slug_cli.py
├── config.yml
├── LICENSE
├── README.md
└── pyproject.toml
```

#### Integration with Documentation Systems

The tool is designed to integrate with systems that use:

- Markdown content
- metadata sidecars
- multilingual documentation
- static site generators

Example workflow:

```
title -> slug -> filename -> metadata sidecar
```

Example:

```
# Référence API
```

Produces:

```
reference-api.md
.reference-api.metadata.yaml
```

### Long-Term Vision

The slug tool may eventually become part of a broader documentation tooling system including:

- metadata generators
- documentation validators
- translation helpers
- CI tooling

Example command suite:

```
slug
doc-meta
doc-check
doc-translate
```

### Development Philosophy

Development follows three phases:

1. **Make it Work**
2. **Make it Right**
3. **Make it Fast**

Version 1 satisfies phase one.

Future work should prioritize **stability and documentation workflows** before expanding functionality.

---

# License

Code: MIT License

Documentation: CC BY-SA 4.0

Copyright (c) 2026 Christopher Steel
