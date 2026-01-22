Use this workflow when starting a new Python project to avoid "Module Not Found" errors.

1. **Confirm project root**
   - Make sure the current working directory is the project root (where your main code and optional `requirements.txt` live).
   - If you’re unsure, list the directory contents and verify you see your project files/folders.

2. **Check for an existing virtual environment (`venv` folder)**
   - List the contents of the current directory.
   - If a directory named `venv` already exists, skip to step 4.

3. **Create a new virtual environment if needed**
   - If `venv` does *not* exist, create it.
   - Prefer `python3` if available, otherwise fall back to `python`.

   // turbo  
   - Run one of the following commands (whichever works on this system):
     - `python3 -m venv venv`
     - `python -m venv venv`
   - If environment creation fails, report the error to the user and stop the workflow.

4. **Activate the virtual environment**
   - Detect the operating system and use the appropriate activation command.

   // turbo  
   - On macOS/Linux (bash/zsh):
     - `source venv/bin/activate`
   - On Windows (PowerShell):
     - `venv\Scripts\Activate.ps1`
   - On Windows (Command Prompt):
     - `venv\Scripts\activate.bat`

   - Confirm the virtual environment is active (e.g. prompt shows `(venv)` or `which python` / `where python` points inside `venv`).

5. **Install dependencies from `requirements.txt` (if present)**
   - Check whether a `requirements.txt` file exists in the current directory.
   - If it does not exist, explain to the user that no dependencies were installed and proceed to step 6.
   - If it exists:

     // turbo  
     - Run: `pip install -r requirements.txt`
     - If installation fails, capture the error output and summarize the most likely causes (e.g. conflicting versions, missing system libraries).

6. **Verify installed packages**
   - Ensure the virtual environment is still active.

   // turbo  
   - Run: `pip list`

   - Confirm that key project packages (if known from the codebase) appear in the list.
   - If important packages seem missing, suggest to the user:
     - Adding them to `requirements.txt`, then
     - Re-running this workflow.

7. **Summarize the environment to the user**
   - Report:
     - Python executable path being used
     - Python version
     - Whether `venv` was newly created or reused
     - Whether `requirements.txt` was found and successfully installed
     - Any errors or warnings encountered along the way
   - Suggest the next step, e.g. running the app’s main script or test suite inside this environment.
