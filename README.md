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

Fast install using slugify 8.0.4

```bash
cd ~/bin
git clone https://github.com/steelcj/tool-python-slugify.git slugify-tool
```

Install bash wrapper to `~/bin`

```bash
cp slugify-tool/scripts/nix/slug .
chmod +x slug
```

```bash
mkdir -p ~/bin/slugify-tool/.venvs/slugify/8.0.4
```

Next we create the tools virtual environment or venv

    python3 -m venv --prompt slugify-8.0.4 ~/bin/slugify-tool/.venvs/slugify/8.0.4

Activate it.

    source ~/bin/slugify-tool/.venvs/slugify/8.0.4/bin/activate

Your prompt should now reflect the activated python venv for the slugify tool

    (slugify-8.0.4)

Install requirements

```bash
pip install -r slugify-tool/requirements.txt
```

## Detailed Installation, Usage and Configuration

[How to Install and Use a Multilingual Slugify Tool with Python Virtual Environment](docs/how-to-install-and-use-the-multilingual-slugify-tool.md)

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
