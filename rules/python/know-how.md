### **Category: Modern Python & Typing**

- Type Safety: "Strictly use Python 3.10+ Type Hints for all function arguments and return values. Use the typing module (e.g., List, Optional) or standard collections."
- Data Validation: "For complex data structures or API payloads, use Pydantic (BaseModel) instead of plain dictionaries. This ensures data validation happens at runtime."
- Explicit Exports: "Use __all__ in modules to explicitly define the public API of a file. This helps the agent understand what functions are meant to be imported elsewhere."

### **Category: Code Style & Quality**

- Docstrings: "Write docstrings using the Google Style Guide format (Args, Returns, Raises). Do not leave functions undocumented."
- Error Handling: "Never use bare except: clauses. Always catch specific exceptions (e.g., except ValueError:). Log errors using the standard logging library, not print()."
- Formatting: "Assume code will be formatted with Black. Do not waste time manually aligning whitespace."

### **Category: Environment & Safety**

- Virtual Env: "Assume code runs inside a virtual environment (venv). Always prefer installing dependencies via pip into the active environment."
- Entry Points: "Always wrap script execution logic in if __name__ == '__main__':. Never let script logic run on import."

### **Category: Deployment & Infrastructure**

- Platform Targets: "Target Render (Web Service) with Python 3.10+. The application must listen on 0.0.0.0 and the port defined by the environment variable PORT (default 10000)."
Start Command: "The start command must be: uvicorn main:app --host 0.0.0.0 --port $PORT. Never use 127.0.0.1 for production."
- Build Command: "The build command is pip install -r requirements.txt. Ensure uvicorn, fastapi, and pydantic are listed in requirements.txt."
- CORS (Critical): "Since this is an API for a separate frontend, you MUST add CORSMiddleware to the FastAPI app. Allow origins from your cPanel domain (or * for dev)."