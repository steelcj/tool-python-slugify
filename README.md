# tool-python-slugify

## Description

A lightweight command-line tool for generating URL-safe slugs from text.

This tool is designed for multilingual documentation workflows and static site
generation systems where consistent slug creation is important for:

- filenames
- URLs
- metadata identifiers
- sidecar metadata files

The tool supports configuration via a YAML file and works naturally with
Unix-style pipelines.

---

## Features

- CLI tool with Unix-style behavior
- Supports both arguments and `stdin`
- Multilingual text support
- Configurable slug rules via YAML
- Uses the `python-slugify` library
- Runs inside an isolated Python virtual environment
- Suitable for static site generators and documentation systems

---

## Installation via github

### Nix's

```bash
cd ~/bin
git clone https://github.com/steelcj/tool-python-slugify.git slugify-tool
```



## Development

Slighly out of date...

## Manual Installation and Creation

### 1. Create the tool directory

```
mkdir -p ~/bin/slugify-tool
```

Place the following files inside the directory:

```
slugify_cli.py
config.yml
```

---

### 2. Create a Python virtual environment

```
python3 -m venv --prompt slugify-8.0.4 ~/.venvs/slugify/8.0.4
```

Install required packages:

```
~/.venvs/slugify/8.0.4/bin/pip install python-slugify==8.0.4 pyyaml
```

---

### 3. Create the wrapper command

Create the command:

```
~/bin/slug
```

Contents:

```bash
#!/usr/bin/env bash

SCRIPT_DIR="$HOME/bin/slugify-tool"

source "$SCRIPT_DIR/.venvs/slugify/8.0.4/bin/activate"

python "$SCRIPT_DIR/slugify_cli.py" "$@"
```

Make it executable:

```
chmod +x ~/bin/slug
```

Ensure `~/bin` is in your PATH.

---

## Usage

### Slugify text

```
slug "Référence API"
```

Output:

```
reference-api
```

---

### Pipe input

```
echo "Accessibilité numérique" | slug
```

Output:

```
accessibilite-numerique
```

---

### File input

```
cat title.txt | slug
```

---

## Configuration

Default behavior is controlled by:

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

### Configuration Options

| Option     | Description                         |
| ---------- | ----------------------------------- |
| lowercase  | Convert text to lowercase           |
| separator  | Character used between words        |
| ascii      | Remove accents and convert to ASCII |
| max_length | Optional maximum slug length        |
| stopwords  | Words to remove from slug           |

---

## Example

Input:

```
slug "Référence de la documentation API"
```

Output:

```
reference-documentation-api
```

---

## Project Structure

Example layout:

```
~/bin/
├── slug
└── slugify-tool/
    ├── config.yml
    ├── slugify_cli.py
    └── __pycache__/
```

---

## Future Improvements

Potential enhancements:

- automatic slug generation from Markdown titles
- multilingual stopword presets config example
- CI integration for documentation workflows (see SAT project)

---

## Dependencies

- Python 3.10+
- python-slugify
- PyYAML

---

## License

This project is released under the MIT License.