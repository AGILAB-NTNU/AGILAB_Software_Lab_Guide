# Conda & Environment Guide

In research, environment configuration (especially involving CUDA and physical simulators) is often the most time-consuming step. This guide defines the unified environment management process for the lab.

## 1. Core Principles

*   **Single Source of Truth**: All dependencies must be recorded in `environment.yml`. Never `pip install` without updating the config file.
*   **Exporting Environments**: When you successfully configure a complex environment, back it up using:
    ```bash
    conda env export --no-builds > environment.yml
    ```

## 2. Handling CUDA Dependencies

To ensure GPU acceleration works correctly, we recommend explicitly specifying the `pytorch-cuda` version in `environment.yml`:

```yaml
dependencies:
  - python=3.9
  - pytorch
  - torchvision
  - pytorch-cuda=12.1  # Explicitly lock CUDA version
  - conda-forge::cupy  # If Cupy is needed
```

## 3. Hardware & Driver Specifications

Due to the strong coupling between CUDA and physical hardware, you **must** document the following hardware baseline in your project's `README.md`:

*   **GPU Model**: Specify the GPU used for the experiment (e.g., `NVIDIA GeForce RTX 4090`). Different architectures (e.g., Turing vs. Ada Lovelace) may affect kernel execution.
*   **Driver Version**: The driver version observed via `nvidia-smi` (e.g., `535.129.03`). This determines the maximum supported CUDA version on the host.
*   **Compute Capability**: If using custom compiled operators, specify the target compute capability (e.g., `sm_89`).

!!! warning "Running code does not guarantee consistent results"
    If significant differences in experimental results are found across different GPUs, check the `Float32` precision and `Tensor Cores` utilization of each card.

## 4. Troubleshooting

### Resolving Dependency Conflicts
If Conda is too slow, try:
1.  **Use Mamba**: `conda install mamba -n base -c conda-forge`. Mamba is a C++ implementation of Conda and is significantly faster.
2.  **Clean Cache**: `conda clean --all`.

### Special Simulator Installation (e.g., Isaac Gym / MuJoCo)
For simulators that cannot be installed directly via Conda:
1.  Create a `third_party/` folder in the project root.
2.  Place the simulator source code there.
3.  Install using `-e third_party/xxx` in the `pip` section of `environment.yml`.
