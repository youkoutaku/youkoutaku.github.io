---
title: Environment manager for Python
date: 2024-12-24 12:00:00 +0900
categories: [Programming, Python]
tags: [Python, venv, Anaconda, uv]
author: Youkoutaku
math: true
image:
  path: https://avatars.githubusercontent.com/u/1525981?s=200&v=4
  alt: Python Github
---

Why do you need an environment manager for Python?

- Version Control
- Ease of Deployment
- Avoiding Conflicts

---
## Python Virtual Environment (venv):
The [venv](https://docs.python.org/3/library/venv.html) module supports creating lightweight “virtual environments”.

```bash
# Create new environment
python -m venv myenv

# Activate environment
# On Windows:
myenv\Scripts\activate
# On Unix/MacOS:
source myenv/bin/activate

# Deactivate environment
deactivate

# Remove the virtual environment (delete the folder)
rm -rf myenv
```

---
## Anaconda
[Anaconda](https://www.anaconda.com/download) provides package, dependency, and environment management for any language.

- Windows: setting system environment variables path
  - C:\ProgramData\anaconda3
  - C:\ProgramData\anaconda3\Library\bin
  - C:\ProgramData\anaconda3\Scripts
  - C:\ProgramData\anaconda3\Lib\site-packages

> restart PC

### Basic Commands
```bash
# Check Anaconda version
conda --version

# Update Anaconda
conda update conda
conda update anaconda
```

####  Disable Automatic Activation
To prevent Conda from being automatically activated as the default terminal environment when you open a terminal, you can run the following command in terminal:

```bash
conda config --set auto_activate_base false
```

### Environment Management
```bash
# Create new environment
conda create --name myenv python=3.11

# Activate environment
conda activate myenv

# List all environment
conda env list

# Deactivate environment
conda deactivate

# Remove an environment
conda remove --name myenv --all
```

### Package Management

```bash
# Install packages
conda install package_name

# Update a package
conda update package_name

# Remove a package
conda remove package_name

# List all installed packages
conda list

# Search for a package
conda search package_name
```

### Environment Export and Import
```bash
# Export an environment
conda env export > environment.yml

# Create an environment from a file
conda env create -f environment.yml
```

---
## uv
[uv](https://docs.astral.sh/uv/) is an extremely fast Python package and project manager, written in Rust.

The steps to use:
1. Install uv
2. Create a new project
3. Create a new environment in the project folder
4. Install the required packages
5. Add the package dependencies
6. Run program with uv

### Install & Using uv

```bash
# Install uv
pip install uv
```

### [Project management](https://docs.astral.sh/uv/guides/projects/)

```bash
# Create new project
uv init new_project

cd new_project

# Create new environment
uv venv

# run a script
uv run hello.py
```

### [Python management](https://docs.astral.sh/uv/guides/install-python/#getting-started)
```bash
# Install multiple Python versions
uv python install 3.10 3.11 3.12

# Set a Python version in the current directory
uv python pin 3.11

# List all python version
uv python list
```

### Install package
```bash
# Install packages (much faster than pip)
uv pip install numpy pandas

# Install from requirements.txt
uv pip install -r requirements.txt
```

### Managing package dependencies
```bash
# Specify a version constraint
uv add 'package_name==2.31.0'

# Add a git dependency
uv add git+https://github.com/package_link

# remove a package
uv remove package_name

# upgrade a package
uv lock --upgrade-package package_name
```

---
## Summary

| Feature                  | venv                       | conda                               | uv                        |
| ------------------------ | -------------------------- | ----------------------------------- | ------------------------- |
| Purpose                  | Basic virtual environments | Complete environment management     | Fast package management   |
| Speed                    | ★★                         | ★                                   | ★★★                       |
| Key Strength             | Built into Python          | Handles all dependencies            | Ultra-fast installations  |
| Package Source           | PyPI only                  | Conda + PyPI                        | PyPI only                 |
| Multiple Python Versions | No                         | Yes                                 | Yes                       |
| Main Use Case            | Single Python projects     | Data Science & Scientific Computing | Modern Python development |

---
