## Debugging a Project

Debugging would be useful when you lack necessary information.
In this section we offer guidance on debugging Python code using various tools.

### Using Python Debugger (pdb)

The Python standard library includes `pdb`, the Python debugger.

```bash
# Run a script with debugger
python -m pdb your_script.py

# Or run pytest with debugger
python -m pytest --pdb
```

Common pdb commands:
- **h**: help - show available commands
- **l** (list): show current position in the code
- **s** (step): execute the current line, step into functions
- **n** (next): execute the current line, step over functions
- **c** (continue): continue until next breakpoint
- **p VARIABLE** (print): print the value of VARIABLE
- **pp VARIABLE** (pretty print): pretty print the value
- **b LINE** (break): set a breakpoint at LINE
- **b FILE:LINE**: set a breakpoint in FILE at LINE
- **cl** (clear): clear all breakpoints
- **q** (quit): quit the debugger

### Using VS Code Debugger

If you're using VS Code, you can use the built-in debugger:

1. Install the Python extension for VS Code
2. Create a `.vscode/launch.json` file in your project root
3. Configure the debugger with appropriate settings

Example `.vscode/launch.json`:

```json
{
    "version": "0.2.0",
    "configurations": [
        {
            "name": "Python: Current File",
            "type": "python",
            "request": "launch",
            "program": "${file}",
            "console": "integratedTerminal",
            "justMyCode": false
        },
        {
            "name": "Pytest: Current File",
            "type": "python",
            "request": "launch",
            "module": "pytest",
            "args": [
                "${file}"
            ],
            "console": "integratedTerminal",
            "justMyCode": false
        }
    ]
}
```

### Using ipdb (Enhanced Debugger)

For a better debugging experience, you can use `ipdb`:

```bash
pip install ipdb
```

Set breakpoints in code:

```python
import ipdb
ipdb.set_trace()  # Set breakpoint
```

Or run with:

```bash
python -m ipdb your_script.py
```

### Using print Statements

For simple debugging, you can use print statements or logging:

```python
import logging

logging.basicConfig(level=logging.DEBUG)
logger = logging.getLogger(__name__)

# In your code
logger.debug(f"Variable value: {variable}")
```

### Debugging pytest Tests

```bash
# Drop into debugger on failure
pytest --pdb

# Drop into debugger on failure, but start with debugger
pytest --trace

# Show local variables on failure
pytest -l

# Use pdb++ for better experience
pip install pdbpp
pytest --pdb
```

### Memory Profiling

For memory issues, you can use memory profilers:

```bash
pip install memory_profiler
python -m memory_profiler your_script.py
```

### Performance Profiling

For performance issues:

```bash
# Using cProfile
python -m cProfile -o profile.stats your_script.py
python -m pstats profile.stats

# Using snakeviz for visualization
pip install snakeviz
snakeviz profile.stats
```

### Commands to Avoid

- DO NOT use `print()` for production logging (use logging instead)
- DO NOT commit debugging code with breakpoints
- DO NOT leave `pdb.set_trace()` or `ipdb.set_trace()` in committed code
