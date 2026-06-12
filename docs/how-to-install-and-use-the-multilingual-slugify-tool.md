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

## Overview

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

## Requirements

### Python venv

```bash
sudo apt install python3-pip
```

### Python pip

```bash
sudo apt install python3-venv
```

## Installation

### Ensure ~/bin Exists

```bash
mkdir -p ~/bin
```

### Ensure ~/bin is in your PATH ENV

```bash
echo $PATH
```

#### If not add it to your startup file

```bash
echo 'export PATH="$HOME/bin:$PATH"' >> ~/.bashrc
```

Reload the shell ENV

```bash
source ~/.bashrc
```

Confirm your path

```bash
echo $PATH
```

## Clone or Create Tool Directory

### Create ~/bin

```bash
mkdir -p ~/bin/
cd ~/bin/
```

### Clone the project

```bash
git clone https://github.com/steelcj/tool-python-slugify.git slugify-tool
```

## Create the tools python virtual environment

By default the tool uses it's own sand boxed Python virtual environment.

Before creating your python venv sandbox it is generally a good idea to check for the latest versions of python-slugify available before doing this in order to support multiple versions if you are in a development environment.

### python-slugify versions

python-slugify is our primary tool so we will do a version check to see the latest available stable version.

Alternatively, If you want to install the latest version used in the repository you can skip this step

```bash
pip index versions python-slugify
```

outyput example:

```bash
python-slugify (8.0.4)
Available versions: 8.0.4, 8.0.3, 8.0.2, 8.0.1, 8.0.0, 7.0.0, 6.1.2, 6.1.1, 6.1.0, 6.0.1, 6.0.0, 5.0.2, 5.0.1, 5.0.0, 4.0.1, 4.0.0, 3.0.6, 3.0.5, 3.0.4, 3.0.3, 3.0.2, 3.0.1, 3.0.0, 2.0.1, 2.0.0, 1.2.6, 1.2.5, 1.2.4, 1.2.3, 1.2.2, 1.2.1, 1.2.0, 1.1.4, 1.1.3, 1.1.2, 1.0.2, 0.1.0, 0.0.9, 0.0.8, 0.0.7, 0.0.6, 0.0.5, 0.0.4, 0.0.3, 0.0.2, 0.0.1
```

Here we see that the latest available version is 8.0.4 so we create a corresponding directory structure for our venv in the slugify tool directory:

```bash
mkdir -p ~/bin/slugify-tool/.venvs/slugify/8.0.4
```

Next we create the tools virtual environment or venv

```bash
python3 -m venv --prompt slugify-8.0.4 ~/bin/slugify-tool/.venvs/slugify/8.0.4
```

Activate it.

```bash
source ~/bin/slugify-tool/.venvs/slugify/8.0.4/bin/activate
```

Your prompt should now reflect the activated python venv for the slugify tool

```
(slugify-8.0.4)
```

## Install the required python libraries

Now that the venv is activated we can install the libraries used by the slugify tool into the venv like this.

```bash
pip install python-slugify
pip install pyyaml
```

These provide:

- robust multilingual slugification
- optional configuration support

## Customising the tool configuration

If you ever want the change the settings you can customise the configuration file

```bash
cd slugify-tool
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

If you want to customise or take a peek at the slugify script.

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
        description="Convert text into a URL-safe slug.",
        epilog=(
            "Tip: use single quotes to safely pass strings containing\n"
            "backticks or other shell-special characters:\n\n"
            "  slug 'What belongs in `trust_boundary.yml`'\n\n"
            "Double quotes do NOT protect backticks from shell expansion."
        ),
        formatter_class=argparse.RawDescriptionHelpFormatter,
    )

    parser.add_argument(
        "text",
        nargs="*",
        help="Text to slugify (use single quotes for special characters)"
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

Save the file if you make any changes.

# The launcher script

We are using a basic launcher script so the tool automatically runs inside the our tools virtual environment.

If you cloned the project you can copy it to your ~/bin directory

```bash
cp scripts/nix/slug ~/bin/.
```

if you want to take a peek at the contents or modify it to reflect the version you are running you can edit it with something like

```bash
nano ~/bin/slug
```

content example:

```bash
#!/usr/bin/env bash

SCRIPT_DIR="$HOME/bin/slugify-tool"

exec "$SCRIPT_DIR/.venvs/slugify/8.0.4/bin/python" \
     "$SCRIPT_DIR/slugify_cli.py" "$@"
```

Alternate example:

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

## Confirmation

Run:

```bash
slug "Changing the Language in GNUCash"
```

Output:

```bash
changing-the-language-in-gnucash
```

### Test With Multilingual Text

```bash
slugify "Accessibilité numérique"
```

Output:

```
accessibilite-numerique
```

### Pipe Input example

```bash
echo "Référence API" | slug
```

Output:

```
reference-api
```



## Use in a documentation workflow

Title in Markdown:

```bash
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

## Optional Configurations (Future)

Because the script uses the `python-slugify` library, it can later support YAML configuration.

Example configuration:

```
separator: "_"
ascii: false
max_length: 80
```

This allows customization of slug behavior for specific projects.

## Use cases

- multilingual static site generators
- documentation repositories
- metadata-driven publishing
- digital archive systems

They can be used to ensure consistent naming conventions across large collections of documents.

## Additional examples

### Default behavior

```
slugify "Référence API"
```

Output

```
reference-api
```

### Pipe

```
echo "Accessibilité numérique" | slugify
```

Output

```
accessibilite-numerique
```

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

## This pattern is widely used in CLI tools because:

• defaults are version-controlled with the script
 • users can override behavior easily
 • no environment variables required
 • it works well for automation and CI

It also makes your tool easier to evolve into:

- a **GUI**
- a **SAT documentation helper**
- a **metadata/slug workflow tool**

## References

<a name="dublin-core-1998-reference"></a>
Dublin Core Metadata Initiative. (1998). *Dublin Core metadata element set, version 1.1*. https://www.dublincore.org/specifications/dublin-core/dces/

[Return to citation](#dublin-core-1998-citation)

<a name="python-slugify-reference"></a>
Tkalec, A. (2024). *python-slugify*. https://github.com/un33k/python-slugify

[Return to citation](#python-slugify-citation)

## License

This document, *How to Install and Use a Multilingual Slugify Tool with Python Virtual Environment*, by **Christopher Steel**, with AI editing assistance from **ChatGPT-5.3 (OpenAI)**, is licensed under the [Creative Commons Attribution-ShareAlike 4.0 License](https://creativecommons.org/licenses/by-sa/4.0/).

![CC License](https://mirrors.creativecommons.org/presskit/buttons/88x31/svg/by-sa.svg)