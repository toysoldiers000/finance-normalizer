# Task

- Check out `Accessing Task Documents` and `Accessing Script Files` in `REPO-ROOT/.github/copilot-instructions.md` for context about mentioned `*.md` and `*.sh`/`*.py` files.
- All `*.md`, `*.sh`, and `*.py` files should exist; you should not create any new files unless explicitly instructed.

## Goal and Constraints

- You must ensure the source code passes linting and type checking.
- You must ensure all tests pass.

## Step 1. Implement Request

- Follow the chat message to implement the task.

## Step 2. Make Sure the Code Passes Checks

- Check out `External Tools Environment and Context` in `REPO-ROOT/.github/copilot-instructions.md` for accessing scripts for building and testing.
  - Strictly follow the instruction above as this repo uses specific tools.
- Each attempt of build-fix process should be executed in a sub agent.
  - One build-fix process includes one attempt with the following instructions.
  - The main agent should call different sub agent for each build-fix process.
  - Do not build and retrieve build results in the main agent.

### Use a sub agent to run all following instructions (`Lint and Type Check`, `Fix Errors`, `Run Tests`, `Fixing Code Change`)

#### Lint and Type Check

- Check out `REPO-ROOT/README.md` to understand project structure and what needs to be checked.
- Run linting tools (ruff, black, pylint, etc.) and type checking (mypy).
- Find out if there is any warning or error.
  - `External Tools Environment and Context` in `REPO-ROOT/.github/copilot-instructions.md` has the instruction about how to check the result.

#### Fix Errors

- If there is any error, address all of them:
  - If there is any linting warning, only fix warnings that caused by your code change. Do not fix any other warnings.
  - If there is any type checking error, you need to carefully identify the issue.
  - For every attempt of fixing the source code:
    - Explain why the original change did not work.
    - Explain what you need to do.
    - Explain why you think it would solve the issue.
- After finishing fixing, exit the current sub agent and tell the main agent to go back to `Step 2. Make Sure the Code Passes Checks`

#### Run Tests

- Check out `REPO-ROOT/README.md` to understand the test structure.
- Run the unit tests using pytest and see if they pass.
  - Make sure added test cases are actually executed.
  - If any test case fails, the error message will be printed to the output.
  - When debugging test failures, you can:
    - Add print statements or use logging
    - Use pytest's -v flag for verbose output
    - Use pytest's -s flag to see print statements
    - Add breakpoints if using a debugger

#### Fix Failed Test Cases

- Identify the cause of test failures by reading the error messages carefully.
- Apply fixes to source files.
- DO NOT delete any test case.
- After finishing fixing, exit the current sub agent and tell the main agent to go back to `Step 2. Make Sure the Code Passes Checks`
  - `Step 2. Make Sure the Code Passes Checks` and test execution should both pass without issues. If you didn't see any progress, the only reason is that your change is not correct.

## Step 3. Verify the Implementation

- Run the full test suite to ensure no regressions.
- Check that the implementation follows the project's coding standards.
- Verify that the implementation matches the requirements in the task description.

## Step 4. Clean Up

- Remove any debug print statements or temporary code.
- Ensure all imports are used and organized properly.
- Verify that the code is ready for review.
