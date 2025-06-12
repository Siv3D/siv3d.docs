# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Overview

This repository contains documentation source files for **Siv3D**, a modern C++ framework for creative coding and game development. The documentation is built using MkDocs with Material theme and published to https://siv3d.github.io/.

## Development Commands

### Setup
```bash
pip install mkdocs-material
```

### Build Documentation
```bash
cd ja-jp/    # Navigate to language directory
mkdocs build # Generates static site in ../../siv3d.github.io/ja-jp/
```

### Development Server
```bash
cd ja-jp/
mkdocs serve  # Live preview with auto-reload
```

## Architecture

### Multi-language Structure
- `ja-jp/` - Japanese documentation (primary, complete)
- `en-us/` - English documentation (secondary, incomplete/in development)
- Each language has its own `mkdocs.yml` configuration and `docs/` content directory

### Content Organization
Documentation follows a progressive learning structure:
- `tutorial/` - Basic concepts (20+ chapters)
- `tutorial2/` - Intermediate topics  
- `tutorial3/` - Advanced features
- `tutorial4/` - Specialized functionality
- `tutorial5/` - Latest additions (work in progress)
- `samples/` - Code examples by category
- `api/` - API reference documentation
- `tools/` - Development utilities and helpers

### Build System
- **MkDocs** generates static HTML from Markdown sources
- **Material theme** with custom Siv3D color scheme
- Output directory: `../../siv3d.github.io/{language}/`
- Uses extensive Markdown extensions for rich content formatting

### Key Configuration
- Site builds are language-specific (separate mkdocs.yml per language)
- Navigation structure defined in YAML configuration
- Custom CSS and assets stored in `docs/assets/` and `docs/stylesheets/`
- Search configured with language-specific tokenization

## Working with This Codebase

- Always work within the appropriate language directory (`ja-jp/` or `en-us/`)
- Test builds locally with `mkdocs serve` before pushing changes
- Markdown files use various extensions (admonitions, code blocks, tables, etc.)
- Images and assets go in `docs/assets/` directory
- The English documentation appears to be undergoing restructuring (many deleted files on current branch)