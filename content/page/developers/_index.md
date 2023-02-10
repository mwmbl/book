---
weight: 3
bookFlatSection: true
title: "Developers"
date: "2023-02-05"
lastmod: "2023-02-05"
---

# Developers Guide

## Local installation

1. We use [poetry](https://python-poetry.org/) to manage
dependencies. Follow the [installation
instructions](https://python-poetry.org/docs/#installation) to install
poetry on your machine.

2. Clone the repo into a local directory and `cd` into the directory.

3. Use `pyenv` to install the right version of python for the project (currently `3.10`):
	```
	brew install pyenv
	pyenv install 3.10
	poetry env use 3.10
	```

4. Enter a poetry shell and install dependencies:
	```
	poetry shell
	poetry install
	```

5. Start the local server against a small index:
	```
	python -m mwmbl.main
	```

6. Make a search request in chrome (or your favorite REST api tool):
   ```
   > http://localhost:5001/search?s=Newton
   ```


## Building your own index

To specify a data directory, run
```
python -m mwmbl.main --data [data_dir]
```
where `[data_dir]` is the path to a directory which will store the
index. If there is no index in this path, it will be created.

