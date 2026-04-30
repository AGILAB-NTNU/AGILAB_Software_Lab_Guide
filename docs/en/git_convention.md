# Git Commit Convention

At AGILAB, we strive for research transparency and traceability. While we avoid overly complex engineering workflows, we maintain a simplified commit message convention to help team members (and your future self) quickly understand the evolution of the code.

## 1. Core Formula
Every commit message should follow this format:

```text
Type: English Description
```

*   **Type**: The category of change.
*   **Description**: A short description starting with a verb.

---

## 2. Common Types

We recommend prioritizing the following 4 common types, but you can expand them as needed:

| Type | Description | Example |
| :--- | :--- | :--- |
| **feat** | New features, experiment modules, or algorithm implementations | `feat: Add attention-based policy network` |
| **fix** | Bug fixes, parameter corrections, or tensor shape alignment | `fix: Resolve gradient explosion in training` |
| **docs** | Documentation updates, README, or code comments | `docs: Update installation steps for CUDA 12` |
| **refactor** | Code refactoring or cleanup (no functional changes) | `refactor: Modularize reward functions` |

### Other Optional Types
If the above four are not sufficient, you may use more types based on the nature of the change, for example:
*   **exp**: Specifically for fine-tuning experiment hyperparameters.
*   **perf**: Performance optimization (e.g., reducing memory usage or speeding up computation).
*   **chore**: Routine tasks (e.g., updating package versions, CI/CD configuration).
*   **test**: Adding or modifying test code.

---

## 3. Golden Rules

1.  **English Only**: All commit messages must be in English to align with international research standards.
2.  **Imperative Mood**: Start descriptions with a verb (e.g., `Add`, `Fix`, `Update`, `Remove`).
3.  **Keep it Concise**: Keep the subject line under 50 characters and omit the trailing period.

---

## 4. Quick Cheat Sheet

*   **New experiment model**: `feat: Add LSTM-based episodic memory`
*   **Fix physical parameters**: `fix: Adjust friction coefficients for simulation`
*   **Update Lab Guide**: `docs: Add Git commit convention guide`
*   **Cleanup redundant code**: `refactor: Remove unused visualization scripts`

---

> [!TIP]
> Good commit logs are the foundation of reproducible research. When you need to trace back an experimental result, a clear log will save you significant time.
