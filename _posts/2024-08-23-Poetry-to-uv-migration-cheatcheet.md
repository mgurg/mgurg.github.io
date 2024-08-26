---
layout: post
title: "Poetry to uv migration cheatcheet"
categories: Python
author: "Michał"
math: false
---

Poetry Cheat Sheet

A simple and easy-to-use cheat sheet if you are migrating from Poetry to ruff.

| Poetry command                  | uv command               | Description                                                |
|---------------------------------|--------------------------|------------------------------------------------------------|
| poetry new [name]               |                          | Creates a new Python project in a new directory.           |
| poetry init                     | uv init                  | Initializes a new Poetry project in the current directory. |
| poetry add [package]            | uv add [package] {--dev} | Adds a new package to the project's dependencies.          |
| poetry remove [package]         | uv remove requests       | Removes a package from the project's dependencies.         |
| poetry show                     |                          | Shows information about the project's packages.            |
| poetry update                   | uv sync                  | Updates the project's dependencies.                        |
| poetry install                  |                          | Installs the project's dependencies.                       |
| poetry run [command]            | uv run main.py           | Runs a command in the virtual environment.                 |
| poetry shell                    |                          | Spawns a shell within the virtual environment.             |
| poetry build                    |                          | Builds the project package.                                |
| poetry publish                  |                          | Publishes the project package to PyPI.                     |
| poetry version [version]        |                          | Bumps the version of the project.                          |
| poetry config [setting] [value] |                          | Configures Poetry settings.                                |
| poetry cache clear --all pypi   | uv cache clean           | Clears Poetry's cache.                                     |


Package with options (like: `fastapi[standard]`)
```bash
uv add fastapi --extra standard
```

uv is now capable of installing and managing Python itself, making it entirely self-bootstrapping:
```bash
uv python install 3.12
```

To create a virtual environment:    

```bash
uv venv
```

`pyproject.toml` - for small, flat projects where You don't need src folder:
```
[tool.hatch.build.targets.wheel]
packages = ["."]
```

Remember to replace [name], [package], [command], [version], [setting], and [value] with your specific project name, package name, command, version number, setting, and value respectively.



