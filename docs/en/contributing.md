# Contributing Guidelines

Welcome to AGILAB! To maintain high research standards and ensure reproducibility, please adhere to the following guidelines when developing projects.

## Quick Start: How to Create a New Project

When starting a new research project, please first refer to the [AGILAB Software Template](https://github.com/AGILAB-NTNU/SoftwareTemplate).

The steps to initialize a new project are as follows:

1. **Clone and Initialize**
   1. Click `Use this template` on GitHub to create a new repository.
   2. Clone the repository to your local environment.
   3. **Rename the core package folder**: Rename `src/project_name/` to your project name (e.g., `src/my_robot_rl/`).
   4. **Update project metadata**: Open `pyproject.toml` and change `name = "project_name"` to your actual project name.

2. **Environment Setup**
   We use Conda to manage complex physical simulations and CUDA dependencies:
   ```bash
   # Create the environment based on environment.yml
   conda env create -f environment.yml
   conda activate agilab_env
   ```

3. **Verify Installation (Crucial)**
   Ensure your package is correctly installed in development mode (Editable mode):
   ```bash
   # Test if the package can be imported in the terminal
   python -c "import your_project_name; print('Installation Successful!')"
   ```

## Lab Workflow

### 1. Branching Strategy
- `main`: Stable branch, containing only code for published papers or official releases.
- `dev`: Integration branch for daily development and experimentation.
- `feature/[name]`: Personal feature or specific experiment branches.

### 2. Development Process
1. **Sync Environment**: Always use `environment.yml` to ensure dependency consistency across the team.
2. **Implement Code**: Write your core logic within `src/project_name/`.
3. **Linting and Formatting**: We use **Ruff**. Ensure your code passes `ruff check .` and `ruff format .` before committing.
4. **Write Tests**: Add or update unit tests in `tests/` for core logic.

### 3. Pull Requests (PRs)
- All PRs should be merged from personal branches into the `dev` branch.
- Ensure all GitHub Actions (CI) tests pass.
- Clearly describe the motivation, methodology, and expected results of the experiment in the PR description.

## Coding Standards

- **Style Guide**: Strictly follow the [Google Python Style Guide].
- **Type Hinting**: All function signatures **MUST** include Type Hinting.
- **Docstrings**: Use Google Style Docstrings for all public modules, classes, and functions.
- **Reproducibility**: Always use random seed locking utilities to ensure stochastic processes are traceable.

## Documentation Maintenance

- If new dependencies are added, update `environment.yml` and `pyproject.toml` immediately.
- If a new experimental pipeline is implemented, provide usage examples in `README.md`.
