# Source File Management

Python projects use a simpler file structure than C++ projects. This project follows the standard Python package structure.

## Project Structure

```
finance_normalizer/
├── finance_normalizer/
│   ├── __init__.py
│   ├── ingest/
│   ├── terminology/
│   ├── constraints/
│   ├── storage/
│   └── orchestration/
├── tests/
├── configs/
├── examples/
├── pyproject.toml
└── requirements.txt
```

## Adding Source Files

- New Python modules should be added to the appropriate package directory
- Add `__init__.py` to make a directory a Python package
- Update imports in related modules as needed
- Add corresponding test files in `tests/` directory

## Renaming Source Files

- Rename the file using your IDE or file system
- Update all imports that reference the renamed file
- Run tests to ensure nothing is broken
- Update documentation if needed

## Removing Source Files

- Delete the file from the file system
- Remove all imports that reference the deleted file
- Run tests to ensure nothing is broken
- Update documentation if needed

## Python Package Conventions

- Use lowercase with underscores for module names (e.g., `excel_reader.py`)
- Use CapWords for class names (e.g., `ExcelReader`)
- Use lowercase with underscores for function and variable names
- Use `__init__.py` to expose package-level API
- Use relative imports within the package (`from . import module`)
- Use absolute imports from outside the package (`from finance_normalizer.ingest import excel_reader`)
