# CHANGELOG

All notable changes to this project will be documented here.

Format follows [Keep a Changelog](https://keepachangelog.com/en/1.0.0/).

---

## [Unreleased]

### Planned
- Fix: backtick and special character handling in bash wrapper
- Feat: Markdown title extraction (`slug file.md`)
- Feat: Sidecar metadata filename generation (`--sidecar`)
- Feat: Multilingual stopword presets

---

## [1.0.0] — 2026-03-16

### Added
- CLI tool `slug` with Unix-style argument and stdin support
- Multilingual text handling via `python-slugify`
- YAML configuration file (`config.yml`)
- Python virtual environment isolation
- Bash wrapper (`scripts/nix/slug`)
- README and ROADMAP
