# LLM-Driven Security Analysis

Traditional static analysis tools have limited capabilities. When they encounter indirect calls or APIs from third-party libraries, call chains and data flows are prone to break, leading to many missed vulnerability reports.

The root cause is that static analysis tools lack semantic understanding. As large language models rapidly evolve, people have realized they already possess semantic reasoning abilities, which inspires us to leverage them.

By using LLMs to complement static analysis tools, we can dramatically improve code analysis accuracy.

## Environment Configuration

Operating system: ubuntu22.04  

Programming language: Python 

Python version: 3.8+

### 1. Install dependencies
```bash
# Enter the repository
cd llm-driven-sec
# Create a virtual environment
python -m venv .venv

# Activate the virtual environment
# Windows
.venv\Scripts\activate.bat
# Linux/Mac
source .venv/bin/activate

# Install dependencies
pip install -r requirements.txt
# Install this project
pip install .
```

### 2. Install submodule dependencies
```bash
# Install the submodule
cd lian
pip install .
```

## Runtime Configuration

### LLM settings
Configure LLM-related parameters in `src/nail/config/config.py`:

```python
########## LLM Config ##########
DEEPSEEK_API_KEY                = "your-api-key"
DEEPSEEK_MODEL                  = "deepseek-chat"
DEEPSEEK_BASE_URL               = "https://api.deepseek.com/v1"
```

### Startup script
```
~/projects/llm-driven-sec/src/nail/main.py
```

### Script arguments
```
run
-f
--noextern
-d
-l
python
-w
~/projects/lian/tests/lian_workspace
<path to the analyzed file/directory>
```

## Project Module Overview

### Core workflow

```
When LIAN encounters an unresolved call break
    ↓
problem_monitor → captures the break and creates the initial Problem
    ↓
task_router → walks the task stack and iteratively handles tasks
    ↓
task_executor → dispatches tasks to the most suitable solver
    ↓
solvers → call Abilities to solve the issue or raise new subtasks
    ↓
task_planner → oversees progress and proposes the next todo task
    ↓
    iterative loop...
    ↓
Initial task finishes, exit the loop
    ↓
result_validator → aligns the result with LIAN
```

### Directory structure
```
llm-driven-sec/
├── src/nail/
│   ├── main.py                    # Project entry point
│   ├── problem_monitor.py         # Interfaces with the static analysis tool, captures breakpoints
│   ├── result_validator.py        # Aligns and feeds results back to the static analysis tool
│   ├── common_structure.py        # Core data structures
│   ├── config/                    # Configuration items
│   ├── tasks/
│   │   ├── task_router.py         # Abstraction of the iterative workflow
│   │   ├── task_planner.py        # Planner that manages progress and todo lists
│   │   ├── task_executor.py       # Task dispatching and result handling
│   │   ├── router_impl.py         # LLM router implementation
│   │   └── planner_impl.py        # LLM planner implementation
│   ├── solvers/
│   │   ├── solver_template.py     # Base template
│   │   ├── method_call_solver.py  # Method-call solver
│   │   ├── import_solver.py       # Import-statement solver
│   │   ├── ...                    # Other solvers
│   │   └── abilities/
│   │       ├── abilities_catalog.py            # Ability mapping table
│   │       ├── abilities_implementations.py    # Ability implementations
│   │       └── abilities_manager.py            # Ability manager
│   └── utils/                    # Utility modules
├── lian/                         # Static analysis tool module
```

