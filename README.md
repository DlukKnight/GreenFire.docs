# GreenFire Documentation

Official documentation for **GreenFire**, a framework designed to simplify the creation of business applications by providing reusable processes, layouts, controls, widgets, and infrastructure while remaining highly extensible and technology-agnostic.

This repository contains the complete documentation website, including guides, API references, concepts, tutorials, and examples.

## Contents

The documentation covers topics such as:

* Getting started
* Backend configuration
* Frontend configuration
* CRUD, Read-only, Wizard, Report and Dashboard processes
* Controls and containers
* Widgets
* Providers
* Builders and helpers
* Infrastructure services
* Scheduler integration
* Audit integration
* Localization
* Core concepts
* Examples and best practices

## Built with

The documentation is generated using **MkDocs Material**.

## Running locally

Install the required dependencies:

```bash
pip install mkdocs-material
```

Start the local documentation server:

```bash
mkdocs serve
```

The documentation will be available at:

```
http://127.0.0.1:8000
```

## Building the site

To generate the static website:

```bash
mkdocs build
```

The generated files will be placed inside the `site/` directory.

## Related repositories

* GreenFire Framework
* GreenFire Frontend
* GreenFire Documentation (this repository)
