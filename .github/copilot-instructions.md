# General Instruction

- `REPO-ROOT` refers to the root directory of the repository.
- `PROJECT-ROOT` refers to the root directory of the Python project (`finance_normalizer/`).
- Project structure and modules can be found in `REPO-ROOT/README.md`.
- Following `Leveraging the Knowledge Base`, find knowledge and documents for this project in `REPO-ROOT/.github/KnowledgeBase/Index.md`.
- Before writing to a source file, read it again and make sure you respect my parallel editing.
- If any `*.prompt.md` file is referenced, take immediate action following the instructions in that file.

## External Tools Environment and Context

- If you are on macOS:
  - Use Python 3.9+ for development
  - Prefer using virtual environments (venv or poetry) for dependency management
  - Use shell scripts in `REPO-ROOT/.github/Scripts/` for common tasks
  - DO NOT call pip install directly in the global environment
  - DO NOT create or delete any file unless explicitly directed
  - MUST run any shell script in this format: `bash absolute-path.sh parameters...` or `python absolute-path.py parameters...`

- If you are on Linux, the same instructions apply as for macOS
- If you are on Windows, you can use WSL or adapt the shell scripts accordingly

## Coding Guidelines and Tools

The Python project in this repo uses standard Python packaging and testing tools.
You must strictly follow the instructions in the following documents,
otherwise it won't work properly.

- Adding/Removing/Renaming Source Files: `REPO-ROOT/.github/Guidelines/SourceFileManagement.md`
- Building/Installing the Project: `REPO-ROOT/.github/Guidelines/Building.md`
- Running the Project:
  - Unit Tests: `REPO-ROOT/.github/Guidelines/Running-UnitTest.md`
  - CLI Application: `REPO-ROOT/.github/Guidelines/Running-CLI.md`
- Debugging a Project: `REPO-ROOT/.github/Guidelines/Debugging.md`
- Testing Framework: pytest and unittest
- Configuration Management: YAML files in `REPO-ROOT/configs/`

## Accessing Task Documents

If you need to find any document for the current working task, they are in the `REPO-ROOT/.github/TaskLogs` folder:
- `Copilot_Scrum.md`
- `Copilot_Task.md`
- `Copilot_Planning.md`
- `Copilot_Execution.md`
- `Copilot_KB.md`

### Important Rules for Writing Markdown Files

- Do not print "````````" or "````````markdown" in a markdown file.
- It is totally fine to have multiple top level `# Topic`.
- When mentioning a Python name in markdown file:
  - If it is defined in the standard Python library or third-party library, use the full name.
  - If it is defined in the source code, use the full name if there is ambiguity, and then mention the file containing its definition.

## Accessing Script Files

If you need to find any script or log files, they are in the `REPO-ROOT/.github/Scripts` folder:
- `copilotPrepare.sh` or `copilotPrepare.py`
- `copilotBuild.sh` or `copilotBuild.py`
- `copilotExecute.sh` or `copilotExecute.py`
- `copilotTest.sh` or `copilotTest.py`
- `Build.log`
- `Execute.log`

## Writing Python Code

- This project uses Python 3.9+, you are recommended to use modern Python features (type hints, dataclasses, etc.)
- All code should be cross-platform (macOS, Linux, Windows). Use `pathlib.Path` for file operations.
- DO NOT MODIFY any code in virtual environment folders (venv/, .venv/, etc.)
- Source code is organized in `finance_normalizer/` directory
- Follow PEP 8 style guide for Python code
- Use 4 spaces for indentation (Python standard)
- Use type hints for function signatures and class attributes
- Use docstrings for modules, classes, and functions
- Prefer composition over inheritance
- Use dataclasses for data containers
- Use pathlib for file path operations instead of os.path
- Use logging module instead of print statements
- Follow the project structure defined in README.md

### Python Type Hints

- Use `typing` module for complex types
- Use `|` syntax for unions in Python 3.10+ (e.g., `str | int` instead of `Optional[str]`)
- Use `TypeAlias` for complex type definitions
- Use `Protocol` for structural subtyping when needed

### Error Handling

- Use specific exceptions instead of generic `Exception`
- Use context managers (`with` statements) for resource management
- Use `finally` blocks for cleanup
- Log errors with appropriate level

### Testing Guidelines

- Write unit tests using pytest
- Use fixtures for test setup
- Use parametrized tests for multiple test cases
- Mock external dependencies using unittest.mock
- Aim for high test coverage but focus on critical paths

## Leveraging the Knowledge Base

- When making design or coding decisions, you must leverage the knowledge base to make the best choice.
- The main entry is `REPO-ROOT/.github/KnowledgeBase/Index.md`, it is organized in this way:
  - `## Guidance`: General guidance for the project
  - `## Project`: Brief description of each module and its purpose
    - `### Choosing APIs`: Guidelines for selecting appropriate APIs
    - `### Design Explanation`: Insights into design decisions
  - `## Experiences and Learnings`: Reflections on the development process
  - `## Python Libraries`: Documentation for relevant Python libraries
    - pandas for Excel manipulation
    - openpyxl for Excel file I/O
    - PyYAML for configuration
    - pytest for testing
    - etc.

### Project/Choosing APIs

There are multiple categories under `Choosing APIs`. Each category begins with a short and accurate title `#### Category`.
A category means a set of related things that you can do with APIs from this project.

Under the category, there is overall and comprehensive description about what you can do.

Under the description, there are bullet points and each item follow the format:  `- Use CLASS-NAME for blahblahblah` (If a function does not belong to a class, you can generate `Use FUNCTION-NAME ...`).
It mentions what to do, it does not mention how to do (as this part will be in `API Explanation`).
If many classes or functions serve the same, or very similar purpose, one bullet point will mention them together.

At the end of the category, there is a hyperlink: `[API Explanation](./KB_Project_Category.md)` (no space between file name, all pascal case).

### Project/Design Explanation

There are multiple topics under `Design Explanation`. Each topic begins with a short and accurate title `#### Topic`.
A topic means a feature of this project, it will be multiple components combined.

Under the topic, there is overall and comprehensive description about what does this feature do.

Under the description, there are bullet points to provide a little more detail, but do not make it too long. Full details are supposed to be in the document from the hyperlink.

At the end of the topic, there is a hyperlink: `[Design Explanation](./KB_Project_Design_Topic.md)` (no space between file name, all pascal case).

### Experiences and Learnings

(to edit ...)
