# Running a CLI Application

This project provides a CLI for normalizing financial statements.

## Running the CLI

```bash
# Activate virtual environment first
source .venv/bin/activate  # On macOS/Linux
# .venv\Scripts\activate  # On Windows

# Run the main CLI
python -m finance_normalizer.orchestration.pipeline \
  --input examples/sample_input.xlsx \
  --constraints configs/constraints.yaml \
  --std-accounts examples/sample_std_accounts.csv \
  --aliases examples/sample_aliases.csv \
  --output out/
```

## Using copilotExecute.sh

You can also use the provided script:

```bash
bash .github/Scripts/copilotExecute.sh
# or
python .github/Scripts/copilotExecute.py
```

## CLI Arguments

- `--input`: Path to input Excel file
- `--constraints`: Path to constraints configuration file
- `--std-accounts`: Path to standard accounts CSV
- `--aliases`: Path to account aliases CSV
- `--output`: Output directory for results

## Output Files

The CLI will generate:
- `out/standardized.xlsx` - Normalized data
- `out/change_events.csv` - Change log
- `out/violations.json` - Constraint violations
- `out/mapping_review_queue.csv` - Unmapped terms for review
