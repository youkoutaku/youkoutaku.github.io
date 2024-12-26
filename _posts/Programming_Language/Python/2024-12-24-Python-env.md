---
title: Environment manager for Python
date: 2024-12-24 12:00:00 +0900
categories: [Programming Language, Python]
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
```

## Anaconda
[Anaconda](https://www.anaconda.com/download) provides package, dependency, and environment management for any language.

- Windows: setting system environment variables path
  - C:\ProgramData\anaconda3
  - C:\ProgramData\anaconda3\Library\bin
  - C:\ProgramData\anaconda3\Scripts
  - C:\ProgramData\anaconda3\Lib\site-packages
> restart pc

```bash
# Create new environment
conda create --name myenv python=3.9

# Activate environment
conda activate myenv

# Deactivate environment
conda deactivate

# Install packages
conda install numpy pandas
```

## uv
[uv](https://docs.astral.sh/uv/) is an extremely fast Python package and project manager, written in Rust.

```bash
# Install uv
pip install uv

# Create new environment
uv venv

# Install packages (much faster than pip)
uv pip install numpy pandas
# Install from requirements.txt
uv pip install -r requirements.txt
```

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
