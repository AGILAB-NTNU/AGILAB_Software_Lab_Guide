# Project Lifecycle Guide

This guide defines the standardized phases of a research project at AGILAB, from "Initiation" to "Archive."

## 1. Initiation Phase

1.  **Create Repo**: Initialize a new repository based on [SoftwareTemplate](https://github.com/AGILAB-NTNU/SoftwareTemplate).
2.  **Set up W&B**: Create a corresponding project in Weights & Biases.
3.  **Define Data Storage**: Determine if large datasets should be stored on server `/data` paths or external storage.

## 2. Development Phase

*   **Atomic Commits**: Habitually `git commit` with descriptive messages.
*   **Sync Protocol**: Notify team members whenever `environment.yml` changes.
*   **Regular Testing**: Ensure `pytest` passes at all times.

## 3. Pre-publication Phase

Before submitting a paper or releasing a preprint:
1.  **Code Cleanup**: Remove all `print` debugging statements; use `logging` instead.
2.  **Documentation Completion**: Ensure README has complete installation and execution examples.
3.  **Set License**: Default recommendation is **MIT License** or **Apache-2.0**.
4.  **Version Tagging**: Tag the current code (e.g., `git tag v1.0-submission`).

## 4. Archive Phase

After paper publication:
1.  **Server Weight Backup**:
    *   Move final model weights (Best Checkpoints) to the unified lab storage path (e.g., `/data/checkpoints/`).
    *   **Naming Convention**: `[ProjectName]_[Date]_[KeyMetric].pt` (e.g., `Humanoid_20240501_reward300.pt`).
2.  **Path Documentation**: Record the **server hostname (or ID)** and the **absolute path** to these weights in the project's README under the "Archive" section (e.g., `Server: G108-A6000, Path: /data/checkpoints/...`).
3.  **Handover Docs**: If there are successors, write a simple `INTERNAL_HANDOVER.md` in `docs/` explaining specific machine paths or hardware interfaces.
