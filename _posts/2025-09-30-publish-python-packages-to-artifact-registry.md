---
layout: post
title: Publish Python Packages to Artifact Registry
---

This document provides a detailed guide on how to structure a Python
project for publication as a package/library using **Poetry** and
distribute it using **Google Cloud Platform (GCP) Artifact Registry**.
It includes best practices, detailed steps, and examples to ensure a
smooth setup and deployment process.

---

### 1. Project Structure

A well-organized Python project using Poetry is crucial for
maintainability and distribution. A recommended structure is as
follows:

```
hello-world/                    # Root directory of the project
├── hello_world/                # Python package source code
│   └── __init__.py             # Package initialization file
├── tests/                      # Unit tests for the package
│   └── test_hello_world.py
├── pyproject.toml              # Poetry configuration and metadata
├── README.md                   # Project description and instructions
└── LICENSE                     # License file
```

### Example `hello_world/__init__.py`

```python
# Example module code
def greet(name: str) -> str:
    return f"Hello, {name}!"

if __name__ == "__main__":
    print(greet("World"))
```

> Tip: Poetry manages dependencies, packaging, and virtual
> environments automatically, simplifying project setup and
> distribution.

---

### 2. Setting Up the Project with Poetry

1. Install Poetry:

```bash
curl -sSL https://install.python-poetry.org | python3 -
```

1. Initialize the project:

```bash
poetry new hello-world
cd hello-world
```

1. Add dependencies:

```bash
poetry add requests numpy
```

1. Activate the virtual environment managed by Poetry:

```bash
poetry shell
```

> Poetry automatically isolates your environment, so you do not need a
> separate .venv setup.

1. To exit the Poetry shell:

```bash
deactivate
```

---

### 3. Managing Dependencies

Poetry uses `pyproject.toml` to manage dependencies. To add a new
dependency:

```bash
poetry add <package_name>
```

To remove a dependency:

```bash
poetry remove <package_name>
```

To install all dependencies in the environment:

```bash
poetry install
```

> Tip: Use poetry lock to generate a poetry.lock file ensuring
> reproducible installs.

---

### 4. Building the Package

1. Build the distribution (source and wheel) with Poetry:

```bash
poetry build
```

This generates files in the `dist/` directory.

---

### 5. Uploading the Package

> Disclaimer: Ensure that you have the necessary permissions to 
> publish packages. Coordinate with your platform or DevOps team to
> confirm repository creation and access before uploading.

Upload using Twine and the build files created by Poetry:

```bash
twine upload \
  --repository-url=https://LOCATION-python.pkg.dev/PROJ-ID/REPO-ID/ \
  -u oauth2accesstoken \
  -p "$(gcloud auth print-access-token)" \
  dist/*
```

For automated authentication, install the keyring helper:

```bash
pip install keyrings.google-artifactregistry-auth
```

This allows Twine to use your `gcloud` credentials automatically.

---

### 6. Installing the Package in a Test Project

Create a new test project to use the published package:

```
test-project/
├── pyproject.toml
├── main_test.py
└── README.md
```

**Example `main_test.py`**:

```python
from hello_world import greet

print(greet("Test Project"))
```

Activate a virtual environment for the test project and install the
package:

```bash
python -m venv .venv
source .venv/bin/activate  # Linux/macOS
# .venv\Scripts\activate  # Windows

TOKEN=$(gcloud auth print-access-token)
REPO_HOST="LOCATION-python.pkg.dev/PROJECT-ID/REPOSITORY-ID/simple"
REPO_URL="https://oauth2accesstoken:${TOKEN}@${REPO_HOST}"
pip install --index-url "${REPO_URL}" hello-world
```

Replace `LOCATION`, `PROJECT-ID`, and `REPOSITORY-ID` with the
appropriate values for your Artifact Registry instance.

> Tip: You can also use keyrings.google-artifactregistry-auth for
> automated authentication instead of manually providing a token.

---

### 7. Best Practices

- Use Poetry to manage dependencies and virtual environments for each
  project.
- Always use `poetry.lock` to ensure reproducible installations.
- Organize unit tests in a dedicated `tests/` directory and run them
  before uploading.
- Follow [Semantic Versioning](https://semver.org/) for version
  control.
- Include a comprehensive `README.md` with usage examples.
- Leverage `keyrings.google-artifactregistry-auth` to avoid handling
  OAuth tokens manually.
- Consider setting up CI/CD pipelines for automated build and
  deployment.
- Coordinate repository URLs and permissions through your
  organization's approved process.

Following these practices ensures that your Python package is
well-structured, isolated, easily distributable, and maintainable for
internal or external users.
