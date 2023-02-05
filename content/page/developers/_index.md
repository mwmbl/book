---
weight: 3
bookFlatSection: true
title: "Developers"
date: "2023-02-05"
lastmod: "2023-02-05"
---

# Developers Guide

## Local installation

We use [poetry](https://python-poetry.org/) to manage
dependencies. Follow the [installation
instructions](https://python-poetry.org/docs/#installation) to install
poetry on your machine.

Clone the repo into a local directory and `cd` into the directory.

Then run
```
poetry shell
poetry install
```
to enter a poetry shell and install dependencies.

Then run
```
python -m mwmbl.main
```
to start a local server running against a small index.

## Building your own index

To specify a data directory, run
```
python -m mwmbl.main --data [data_dir]
```
where `[data_dir]` is the path to a directory which will store the
index. If there is no index in this path, it will be created.

