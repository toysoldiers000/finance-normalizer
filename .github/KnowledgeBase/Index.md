# Finance Normalizer Knowledge Base

## Guidance

This project follows the principle of separating concerns between:
1. **Constraint & Repair Engine** - Handles numerical relationships and data fixing
2. **Terminology Alignment Engine** - Handles mapping account names to standard IDs
3. **Orchestration Layer** - Coordinates the pipeline

Key principles:
- Keep terminology alignment separate from constraint repair
- All automatic changes must be logged
- Mapping libraries should be versionable
- LLM assistance is optional and constrained

## Project

### Finance Normalizer

A Python-based tool for normalizing financial statements from Excel to a standardized template.

### Choosing APIs

#### Excel File I/O

- Use `openpyxl` for reading and writing Excel files in .xlsx format
- Use `pandas.read_excel()` for quick data extraction with automatic type inference
- Use `pandas.ExcelWriter` for writing multiple sheets to a single file

[API Explanation](./KB_Pandas_ExcelIO.md)

#### Data Manipulation

- Use `pandas.DataFrame` for tabular data manipulation
- Use `pandas.Series` for single column operations
- Use `numpy` for numerical operations and array handling

[API Explanation](./KB_Pandas_DataFrame.md)

#### Configuration

- Use `yaml.safe_load()` for reading YAML configuration files
- Use `yaml.dump()` for writing YAML files

[API Explanation](./KB_PyYAML.md)

#### Testing

- Use `pytest` for unit testing
- Use `pytest fixtures` for test setup
- Use `unittest.mock` for mocking external dependencies

[API Explanation](./KB_Pytest.md)

#### Type Hints

- Use `typing` module for complex types (List, Dict, Optional, etc.)
- Use `|` syntax for unions in Python 3.10+
- Use `TypeAlias` for complex type definitions

[API Explanation](./KB_Typing.md)

#### Logging

- Use `logging` module for structured logging
- Use different log levels (DEBUG, INFO, WARNING, ERROR)
- Configure log format and handlers in the application

[API Explanation](./KB_Logging.md)

### Design Explanation

#### Tree Structure for Financial Data

Financial data has hierarchical structure where:
- Parent nodes represent aggregated categories
- Child nodes represent individual line items
- Indentation level determines the hierarchy

We use a tree structure to:
- Preserve the parent-child relationships
- Enable bottom-up aggregation (children → parent)
- Enable top-down filling (parent → children)

[Design Explanation](./KB_Design_TreeStructure.md)

#### Constraint System

Constraints define numerical relationships:
- **SUM**: Parent = sum of children
- **EQUAL**: Parent ≡ child (when only one child exists)
- **CUSTOM**: User-defined constraints

The constraint engine:
1. Iterates until convergence
2. Logs all changes for auditability
3. Supports multiple repair strategies

[Design Explanation](./KB_Design_ConstraintSystem.md)

#### Terminology Alignment

Account names have variations that need to be normalized:
1. **Exact match** - Lookup in alias table
2. **Fuzzy match** - String similarity matching
3. **Vector similarity** - Semantic similarity (optional)
4. **LLM assistance** - Constrained ranking (optional)
5. **Human review** - Final approval

[Design Explanation](./KB_Design_TerminologyAlignment.md)

## Python Libraries

### pandas

Data manipulation library for structured data.

[API Explanation](./KB_Pandas_DataFrame.md)

### openpyxl

Library for reading and writing Excel .xlsx files.

[API Explanation](./KB_Openpyxl_Workbook.md)

### PyYAML

YAML parser and emitter for Python.

[API Explanation](./KB_PyYAML.md)

### pytest

Testing framework for Python.

[API Explanation](./KB_Pytest.md)

### typing

Type hints support for Python.

[API Explanation](./KB_Typing.md)

## Experiences and Learnings

(to be filled in as the project progresses)
