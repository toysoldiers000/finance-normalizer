# Verifying

- Check out `Accessing Task Documents` and `Accessing Script Files` in `REPO-ROOT/.github/copilot-instructions.md` for context about mentioned `*.md` and `*.sh`/`*.py` files.
- All `*.md`, `*.sh`, and `*.py` files should exist; you should not create any new files unless explicitly instructed.
  - The `Copilot_Execution.md` file should already exist.
  - If you cannot find the file, you are looking at a wrong folder.
- Following `Leveraging the Knowledge Base` in `REPO-ROOT/.github/copilot-instructions.md`, find knowledge and documents for this project in `REPO-ROOT/.github/KnowledgeBase/Index.md`.

## Goal and Constraints

- All instructions in `Copilot_Execution.md` should have been applied to the source code, your goal is to test it.
- You must ensure the source code passes linting and type checking.
- You must ensure all tests pass.
- Until the code passes checks and all test cases pass, ensure there is a `# !!!VERIFIED!!!` mark at the end of `Copilot_Execution.md`.

## Step 1. Check and Respect my Code Change

- If you spot any difference between `Copilot_Execution.md` and the source code:
  - It means I edited them. I have my reason. DO NOT change the code to match `Copilot_Execution.md`.
  - Write down every difference you spotted, make a `## User Update Spotted` section in the `# UPDATES` section in `Copilot_Execution.md`.

## Step 2. Make Sure the Code Passes Checks

- Check out `External Tools Environment and Context` in `REPO-ROOT/.github/copilot-instructions.md` for accessing scripts for building and testing.
  - Strictly follow the instruction above as this repo uses specific tools.
- Each attempt of build-fix process should be executed in a sub agent.
  - One build-fix process includes one attempt with the following instructions.
  - The main agent should call different sub agent for each build-fix process.
  - Do not build and retrieve build results in the main agent.

### Use a sub agent to run all following instructions (`Lint and Type Check`, `Fix Errors`, `Run Tests`, `Finishing Code Change`)

#### Lint and Type Check

- Check out `# AFFECTED MODULES` in `Copilot_Execution.md` to find out what modules you need to check.
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
    - Log these in `Copilot_Execution.md`, with section `## Fixing attempt No.<attempt_number>` in `# FIXING ATTEMPTS`.

#### Run Tests

- Check out `REPO-ROOT/README.md` to understand the test structure.
- Run the unit tests using pytest.
  - Make sure the tests are relevant to the modules being modified.
  - If any test case fails, the error message will be printed to the output.
  - When debugging test failures, you can use logging or pytest's debug features.

#### Finishing Code Change

- Exit the current sub agent and tell the main agent to go back to `Step 2. Make Sure the Code Passes Checks`.
  - If there is any unfinished work, make sure to tell the main agent about them.

## Step 3. Run Unit Test

- Check out `External Tools Environment and Context` in `REPO-ROOT/.github/copilot-instructions.md` for accessing scripts for testing and debugging.
  - Strictly follow the instruction above as this repo uses specific tools.
- Each attempt of test-fix process should be executed in a sub agent.
  - One test-fix process includes one attempt following `Execute Unit Test` and `Fix Failed Test Cases`.
  - The main agent should call different sub agent for each test-fix process.
  - Do not test and retrieve test results in the main agent.

### Use a sub agent to run the following instructions (`Execute Unit Test`, `Identify the Cause of Failure`, `Fix Failed Test Cases`)

#### Execute Unit Test

- Check out `# AFFECTED MODULES` in `Copilot_Execution.md` to find out what modules you need to test.
- Run the unit tests using pytest and see if they pass.
  - Make sure added test cases are actually executed.
  - If any test case fails, the error message will be printed to the output.
  - When debugging test failures, you can:
    - Add print statements or use logging
    - Use pytest's -v flag for verbose output
    - Use pytest's -s flag to see print statements
    - Add breakpoints if using a debugger

#### Identify the Cause of Failure

- You can refer to `Copilot_Task.md` and `Copilot_Planning.md` to understand the context, keep the target unchanged.
- Dig into related source code carefully, make your assumption about the root cause.
- Use logging or debugging tools to help.
  - They can make sure the expected code path is executed.
  - They can print variable values after converting to strings.
- Debug the unit test directly to get accurate clues if you are not confident of the assumption
  - Follow `Debugging a Project` to start a debugger.
  - From there you can set breakpoints, walk through code by lines, and inspect variables.
  - You must stop the debugger after you finish debugging.
- When you have made a few guesses but did not progress, you are recommended to debug the unit test directly.
  - Breakpoints are very useful to ensure the expected code path is executed, and you can inspect variable values.

#### Fix Failed Test Cases

- Apply fixes to source files.
- DO NOT delete any test case.
- For every attempt of fixing the source code:
  - Explain why the original change did not work.
  - Explain what you need to do.
  - Explain why you think it would solve the issue.
  - Log these in `Copilot_Execution.md`, with section `## Fixing attempt No.<attempt_number>` in `# FIXING ATTEMPTS`.
- After finishing fixing, exit the current sub agent and tell the main agent to go back to `Step 2. Make Sure the Code Passes Checks`
  - `Step 2. Make Sure the Code Passes Checks` and `Step 3. Run Unit Test` are absolutely no problem. If you didn't see any progress, the only reason is that your change is not correct.

## Step 4. Check it Again

- Go back to `Step 2. Make Sure the Code Passes Checks`, follow all instructions and all steps again.
