# Execution

- Check out `Accessing Task Documents` and `Accessing Script Files` in `REPO-ROOT/.github/copilot-instructions.md` for context about mentioned `*.md` and `*.sh`/`*.py` files.
- All `*.md`, `*.sh`, and `*.py` files should exist; you should not create any new files unless explicitly instructed.
  - The `Copilot_Execution.md` file should already exist.
  - If you cannot find the file, you are looking at a wrong folder.
- Following `Leveraging the Knowledge Base` in `REPO-ROOT/.github/copilot-instructions.md`, find knowledge and documents for this project in `REPO-ROOT/.github/KnowledgeBase/Index.md`.

## Goal and Constraints

- You are going to apply changes to the source code following `Copilot_Execution.md`.

## Copilot_Execution.md Structure

- `# !!!EXECUTION!!!`: This file always begins with this title.
- `# UPDATES`:
  - `## UPDATE`: There could be multiple occurrences. Each one has an exact copy of the update description I gave you.
- `# AFFECTED MODULES`.
- `# EXECUTION PLAN`.
- `# FIXING ATTEMPTS`.

## Step 1. Identify the Problem

- The execution document is in `Copilot_Execution.md`
- Find `# Update` in the LATEST chat message.
  - Ignore any of these titles in the chat history.

### Execute the Plan (only when no title appears in the LATEST chat message)

Ignore this section if there is any title in the LATEST chat message
I am starting a fresh new request.

- Apply all code changes in `Copilot_Execution.md` to the source code.
  - Make sure indentation and line breaks are applied correctly, following the same style in the target file.
- After applying each step in `Copilot_Execution.md`, mark the step as completed by appending `[DONE]` after the step title. This allows you to find where you are if you are interrupted.

### Update the Source Code and Document (only when "# Update" appears in the LATEST chat message)

Ignore this section if there is no "# Update" in the LATEST chat message
I am going to propose some change to the source code.

- Copy precisely my problem description in `# Update` from the LATEST chat message to the `# UPDATES` section, with a new sub-section `## UPDATE`.
- Follow my update to change the source code.
- Update the document to keep it consistent with the source code.

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

## Step 4. Verify Coding Style

- Code changes in `Copilot_Execution.md` may not have correct indentation and coding style.
  - Go over each code change and ensure:
    - Indentation is correct and consistent with the surrounding code (4 spaces for Python).
    - Coding style especially line breaks follows the same conventions as the surrounding code.
    - Type hints are properly formatted.
    - Docstrings follow the project's docstring style.
- Ensure any empty line does not contain spaces or tabs.
