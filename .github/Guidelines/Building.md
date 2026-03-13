# Building/Installing the Project

This project uses Python and standard Python packaging tools.

## Setting Up the Environment

### Create a Virtual Environment

```bash
# In the project root
python -m venv .venv
source .venv/bin/activate  # On macOS/Linux
# .venv\Scripts\activate  # On Windows
```

### Install Dependencies

```bash
# Install the project in development mode
pip install -e .

# Or install from requirements.txt
pip install -r requirements.txt
```

## Using copilotBuild.sh

Only run `copilotBuild.sh` to install dependencies and run linting/type checking.

```bash
cd REPO-ROOT
bash .github/Scripts/copilotBuild.sh
# or
python .github/Scripts/copilotBuild.py
```

## The Correct Way to Read Build Result

- The only source of trust is the raw output of the linter/type checker.
- Wait for the script to finish before reading the log file.
  - Building takes a long time. DO NOT hurry.
  - When the script finishes, the result is saved to `REPO-ROOT/.github/Scripts/Build.log`.
  - A temporary file `Build.log.unfinished` is created during building. It will be automatically deleted as soon as the building finishes. If you see this file, it means the building is not finished yet.
- When the check succeeds, you should see:
  - No errors from mypy (type checking)
  - No errors from ruff/black (linting/formatting)
  - "All checks passed" or similar message
- DO NOT delete the log file by yourself unless explicitly required.

## Linting and Type Checking

You can also run these tools individually:

```bash
# Type checking with mypy
mypy finance_normalizer/

# Linting with ruff
ruff check finance_normalizer/

# Formatting with black (check only)
black --check finance_normalizer/

# Formatting with black (apply changes)
black finance_normalizer/
```
