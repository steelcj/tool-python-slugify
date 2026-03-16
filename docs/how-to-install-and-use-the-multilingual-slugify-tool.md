---
Title: "How to Install and Use the Multilingual Slugify Tool"
Description: "Step-by-step instructions for installing a cross-platform Python slugification tool for multilingual documentation workflows, including manual virtual environment setup and CLI usage."
Author: "Christopher Steel"
Date: "2026-03-16"
Last_Modified_Date: "2026-03-16"
License: "CC BY-SA 4.0"
Tags:
  - "slugify"
  - "python"
  - "virtual environment"
  - "documentation tools"
  - "multilingual development"
URL: "https://universalcake.com/resources/tools/python-slugify-cli-setup"
Path: "resources/tools/python-slugify-cli-setup"
Canonical: "https://universalcake.com/resources/tools/python-slugify-cli-setup"
Sitemap: "true"
Keywords:
  - "slugify python cli"
  - "multilingual slugification"
  - "python venv tutorial"
  - "documentation tooling"
  - "markdown workflow"
DC_Title: "How to Install and Use a Multilingual Slugify Tool with Python Virtual Environment"
DC_Creator: "Christopher Steel"
DC_Subject: "Python slugification tooling for multilingual documentation systems"
DC_Description: "Instructions for installing a slugification tool using Python virtual environments for multilingual documentation development."
DC_Language: "en"
DC_License: "https://creativecommons.org/licenses/by-sa/4.0/"
Robots: "index, follow"
OG_Title: "Install a Multilingual Python Slugify CLI Tool"
OG_Description: "A practical guide to installing a slugify command line tool for multilingual documentation workflows."
OG_URL: "https://universalcake.com/resources/tools/python-slugify-cli-setup"
OG_Image: ""
Schema:
  "@context": "https://schema.org"
  "@type": "TechArticle"
  "name": "How to Install and Use a Multilingual Slugify Tool with Python Virtual Environment"
  "author":
    "@type": "Person"
    "name": "Christopher Steel"
Video_Metadata:
  "none": "none"
---

# How to Install and Use a Multilingual Slugify Tool with Python Virtual Environment

This guide explains how to install a **cross-platform slugification tool** for multilingual documentation development.

The tool converts titles into **URL-safe slugs** that work well for:

- static sites
- multilingual documentation
- Markdown content workflows
- metadata-driven publishing systems

The instructions assume a Linux or Unix-like system but the Python components work on all platforms.

---

# Overview

The tool will:

1. Read text from the command line or stdin.
2. Normalize accents and multilingual characters.
3. Generate a filesystem-safe slug.
4. Work within a Python virtual environment.

Example:

```
Référence API
```

becomes

```
reference-api
```

---

# Install Location

The script will be installed in:

```
~/bin
```

This location is commonly used for personal command-line tools.

---

# Step 1 — Ensure ~/bin Exists

```
mkdir -p ~/bin
```

Add it to your PATH if necessary.

```
echo 'export PATH="$HOME/bin:$PATH"' >> ~/.bashrc
```

Reload the shell.

```
source ~/.bashrc
```

Verify:

```
echo $PATH
```

---

# Step 2 — Create Tool Directory

Create a directory for the slugify tool.

```ls- al 
mkdir -p ~/bin/slugify-tool
cd ~/bin/slugify-tool
```

---

# Step 3 — Create Python Virtual Environment

Figure out which version will be installed

```bash
pip index versions python-slugify
```

outyput example:

```bash
WARNING: pip index is currently an experimental command. It may be removed/changed in a future release without prior warning.
python-slugify (8.0.4)
Available versions: 8.0.4, 8.0.3, 8.0.2, 8.0.1, 8.0.0, 7.0.0, 6.1.2, 6.1.1, 6.1.0, 6.0.1, 6.0.0, 5.0.2, 5.0.1, 5.0.0, 4.0.1, 4.0.0, 3.0.6, 3.0.5, 3.0.4, 3.0.3, 3.0.2, 3.0.1, 3.0.0, 2.0.1, 2.0.0, 1.2.6, 1.2.5, 1.2.4, 1.2.3, 1.2.2, 1.2.1, 1.2.0, 1.1.4, 1.1.3, 1.1.2, 1.0.2, 0.1.0, 0.0.9, 0.0.8, 0.0.7, 0.0.6, 0.0.5, 0.0.4, 0.0.3, 0.0.2, 0.0.1
```

then:

```bash
mkdir -p .venvs/slugify/8.0.4
```

Create the environment manually.

```bash
python3 -m venv --prompt slugify-8.0.4 .venvs/slugify/8.0.4
```

Activate it.

```bash
source .venvs/slugify/8.0.4/bin/activate
```

Your prompt should now show:

```
(slugify-8.0.4)
```

---

# Step 4 — Install Required Libraries

Install the libraries used by the slugify tool.

```bash
pip install python-slugify
pip install pyyaml
```

These provide:

- robust multilingual slugification
- optional configuration support

---

# Step 5 — Create the Python Script

### Optional Custom Config

Create the config file

```bash
nano config.yml
```

Content example:

```yaml
lowercase: true
separator: "-"
ascii: true
max_length: 80
stopwords: []
```

Create the slugify script.

```
nano slugify_cli.py
```

Paste the following:

```python
#!/usr/bin/env python3

import sys
import argparse
import yaml
from pathlib import Path
from slugify import slugify


# ------------------------------------------------------------------------------
# Built-in defaults (fallback)
# ------------------------------------------------------------------------------

DEFAULT_CONFIG = {
    "lowercase": True,
    "separator": "-",
    "ascii": True,
    "max_length": None,
    "stopwords": [],
}


# ------------------------------------------------------------------------------
# Load config from script directory
# ------------------------------------------------------------------------------

def load_script_config():

    script_dir = Path(__file__).resolve().parent
    config_file = script_dir / "config.yml"

    if config_file.exists():
        with open(config_file, "r", encoding="utf-8") as f:
            return yaml.safe_load(f) or {}

    return {}


# ------------------------------------------------------------------------------
# Load user config
# ------------------------------------------------------------------------------

def load_user_config(path):

    if not path:
        return {}

    try:
        with open(path, "r", encoding="utf-8") as f:
            return yaml.safe_load(f) or {}

    except Exception as e:
        print(f"Error loading config: {e}", file=sys.stderr)
        sys.exit(1)


# ------------------------------------------------------------------------------
# Merge configs
# ------------------------------------------------------------------------------

def build_config(user_config):

    config = DEFAULT_CONFIG.copy()

    config.update(load_script_config())
    config.update(user_config)

    return config


# ------------------------------------------------------------------------------
# Input handling
# ------------------------------------------------------------------------------

def read_input(args):

    if args:
        return " ".join(args)

    if not sys.stdin.isatty():
        data = sys.stdin.read().strip()
        if data:
            return data

    return None


# ------------------------------------------------------------------------------
# CLI
# ------------------------------------------------------------------------------

def main():

    parser = argparse.ArgumentParser(
        description="Convert text into a URL-safe slug."
    )

    parser.add_argument(
        "text",
        nargs="*",
        help="Text to convert"
    )

    parser.add_argument(
        "-c",
        "--config",
        help="Optional user config YAML"
    )

    args = parser.parse_args()

    text = read_input(args.text)

    if not text:
        parser.print_help()
        sys.exit(0)

    user_config = load_user_config(args.config)
    config = build_config(user_config)

    result = slugify(
        text,
        lowercase=config["lowercase"],
        separator=config["separator"],
        max_length=config["max_length"],
        stopwords=config["stopwords"],
        allow_unicode=not config["ascii"],
    )

    print(result)


if __name__ == "__main__":
    main()
```

Save the file.

---

# Step 6 — Make Script Executable

```
chmod +x slugify.py
```

---

# Step 7 — Create a Launcher Script

Create a simple launcher so the tool automatically runs inside the virtual environment.

```
nano ~/bin/slug
```

Paste:

```bash
#!/usr/bin/env bash

SCRIPT_DIR="$HOME/bin/slugify-tool"

source "$SCRIPT_DIR/.venvs/slugify/8.0.4/bin/activate"

python "$SCRIPT_DIR/slugify_cli.py" "$@"
```

Save and make executable:

```bash
chmod +x ~/bin/slug
```

---

# Step 8 — Test the Tool

Run:

```
slug "Changing the Language in GNUCash"
```

Output:

```
changing-the-language-in-gnucash
```

---

# Test With Multilingual Text

```
slugify "Accessibilité numérique"
```

Output:

```
accessibilite-numerique
```

---

# Pipe Input

```bash
echo "Référence API" | slug
```

Output:

```
reference-api
```

---

# Example Use in Documentation Workflow

Title in Markdown:

```
# Métadonnées Dublin Core
```

Slug generated:

```
metadonnees-dublin-core
```

Filename:

```
metadonnees-dublin-core.md
```

---

# Optional Configuration (Future)

Because the script uses the `python-slugify` library, it can later support YAML configuration.

Example configuration:

```
separator: "_"
ascii: false
max_length: 80
```

This allows customization of slug behavior for specific projects.

---

# When This Tool Is Useful

Slug generation tools like this are useful for:

- multilingual static site generators
- documentation repositories
- metadata-driven publishing
- digital archive systems

They ensure consistent naming conventions across large collections of documents.

# Example Usage

### Default behavior

```
slugify "Référence API"
```

Output

```
reference-api
```

------

### Pipe

```
echo "Accessibilité numérique" | slugify
```

Output

```
accessibilite-numerique
```

------

### Override config

```
slugify --config myconfig.yml "Accessibilité numérique"
```

Example `myconfig.yml`

```
ascii: false
separator: "_"
```

Output

```
accessibilité_numérique
```

------

# Why This Pattern Works Well

This pattern is widely used in CLI tools because:

• defaults are version-controlled with the script
 • users can override behavior easily
 • no environment variables required
 • it works well for automation and CI

It also makes your tool easier to evolve into:

- a **GUI**
- a **SAT documentation helper**
- a **metadata/slug workflow tool**

---

# References

<a name="dublin-core-1998-reference"></a>
Dublin Core Metadata Initiative. (1998). *Dublin Core metadata element set, version 1.1*. https://www.dublincore.org/specifications/dublin-core/dces/

[Return to citation](#dublin-core-1998-citation)

<a name="python-slugify-reference"></a>
Tkalec, A. (2024). *python-slugify*. https://github.com/un33k/python-slugify

[Return to citation](#python-slugify-citation)

---

## License

This document, *How to Install and Use a Multilingual Slugify Tool with Python Virtual Environment*, by **Christopher Steel**, with AI assistance from **ChatGPT-5.3 (OpenAI)**, is licensed under the [Creative Commons Attribution-ShareAlike 4.0 License](https://creativecommons.org/licenses/by-sa/4.0/).

![CC License](https://mirrors.creativecommons.org/presskit/buttons/88x31/svg/by-sa.svg)