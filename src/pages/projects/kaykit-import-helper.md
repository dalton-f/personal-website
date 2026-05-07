---
layout: ../../layouts/MarkdownPostLayout.astro
title: "KayKit Import Helper"
publicationDate: "2026-04-23"
description: "A lightweight Godot plugin designed to streamline workflows with KayKit asset packs"
---

KayKit Import Helper is a fully open-sourced and lightweight Godot plugin designed to streamline workflows with KayKit asset packs.

It reduces the repetitive manual work involved in setting up assets by standardizing imports, folder structures, and material generation.

## Why I Built It

After buying the KayKit Complete Collection of assets, I found myself repeatedly:

- Reorganizing folders
- Renaming files
- Fixing import settings
- Recreating material setups

The process quickly became tedious and time-consuming, especially when prototyping new projects.

To streamline my workflow, I built this tool to automate the entire import and organisation pipeline, allowing assets to be prepared consistently and much faster directly inside the Godot Engine editor.

## How It Works

This tool is implemented as a custom EditorPlugin for Godot Engine, featuring a dedicated dock integrated directly into the editor.

The dock allows users to validate directories containing imported assets and progressively reimport them in bulk.

The reimporter automatically:

- Creates output directories with consistent folder structures
- Extracts and replaces materials from .gltf files
- Moves and renames files for cleaner project organisation
- Fixes texture URIs used in .gltf asset previews
- Adjusts texture compression settings to reduce visual banding
- Generates GridMap resources by instantiating models into a grid

The tool is designed to process multiple import directories simultaneously, making it suitable for large-scale asset workflows, even if processing can take some time.

To improve usability and debugging, the plugin uses print_rich for clear progress tracking and provides an intuitive dock interface for adjusting reimport settings directly within the editor.

## Challenges

One of the main challenges was working with imported files while safely refreshing the editor filesystem without introducing asynchronous issues, as well as correctly handling and fixing texture URIs.

Another challenge was designing the importer so it remained flexible without becoming overly configuration-heavy.

This project took multiple months to complete, with help from other developers who designed similar plugins. A big thank you to the KayKit community and especially Syvies and Dragon1Freak for their help!

## What I Learned

This project improved my understanding of:

- Developing custom EditorPlugins in Godot Engine
- Building efficient automation workflows
- Writing synchronous filesystem scripts
- Designing maintainable, well-documented developer tools
- Applying separation of concerns through organized code regions

## Future Improvements

Potential areas for future development include:

- A more polished and intuitive GUI
- Configurable import rules and presets
- Expanded GridMap configuration options
- Performance optimizations for larger asset pipelines
- Public release on the Godot plugin store

## Links

- [KayKit Import Helper Repository](https://github.com/dalton-f/kaykit-import-helper?utm_source=chatgpt.com)
- [KayKit Asset Pack](https://kaylousberg.itch.io/?utm_source=chatgpt.com)
- [Advanced Model Import by Syvies](https://github.com/Syvies/godot-plugin-advanced-model-import)
- [Bulk Model Manager by Dragon1Freak](https://github.com/dragon1freak/godot-bulk-model-manager)
