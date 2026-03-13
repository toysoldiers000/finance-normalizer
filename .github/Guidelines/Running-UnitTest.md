# Running Unit Tests

This project uses pytest for testing.

## Using copilotTest.sh

Only run `copilotTest.sh` to run tests.

```bash
cd REPO-ROOT
bash .github/Scripts/copilotTest.sh
# or
python .github/Scripts/copilotTest.py
```

## Running Tests Manually

You can also run pytest directly:

```bash
# Run all tests
pytest

# Run tests in a specific directory
pytest tests/

# Run tests in a specific file
pytest tests/test_constraints.py

# Run a specific test
pytest tests/test_constraints.py::TestSumRule::test_aggregation

# Run with verbose output
pytest -v

# Run with print statements shown
pytest -s

# Run and stop on first failure
pytest -x

# Run with coverage
pytest --cov=finance_normalizer
```

## The Correct Way to Read Test Result

- The only source of trust is the raw output of the pytest process.
- Wait for the script to finish before reading the log file.
  - DO NOT need to read the output from the script.
  - Testing takes a long time. DO NOT hurry.
  - When the script finishes, the result is saved to `REPO-ROOT/.github/Scripts/Execute.log`.
  - A temporary file `Execute.log.unfinished` is created during testing. It will be automatically deleted as soon as the testing finishes. If you see this file, it means the testing is not finished yet.
- When all test cases pass, the last several lines of `Execute.log` should show:
  - "X passed in Y.XXs"
  - Or similar success message from pytest
- DO NOT delete the log file by yourself unless explicitly required.

## Test Organization

Tests are organized in the `tests/` directory:
- `tests/test_constraints/` - Tests for constraint engine
- `tests/test_terminology/` - Tests for terminology alignment
- `tests/test_orchestration/` - Tests for pipeline orchestration
- `tests/test_ingest/` - Tests for Excel ingestion
- `tests/test_storage/` - Tests for database/storage

## Writing Tests

When writing new tests, follow these conventions:
- Use descriptive test names
- Use fixtures for common setup
- Use parametrized tests for multiple similar cases
- Mock external dependencies (file I/O, database, etc.)
- Add type hints to test functions
- Keep tests independent and isolated

Example test structure:

```python
import pytest
from finance_normalizer.constraints import SumRule

class TestSumRule:
    def test_aggregation(self, sample_tree):
        """Test that sum rule correctly aggregates child values."""
        result = SumRule.apply(sample_tree)
        assert result.total == expected_value

    @pytest.mark.parametrize("input_value,expected", [
        (1, 1),
        (2, 2),
        (3, 3),
    ])
    def test_parametrized(self, input_value, expected):
        assert process(input_value) == expected
```
