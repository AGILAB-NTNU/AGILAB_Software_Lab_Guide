# AGILAB Software Lab Guide (Internal)

This repository contains the source code for the [AGILAB Software Lab Guide](https://agilab-ntnu.github.io/AGILAB_Software_Lab_Guide/).

## Maintenance

The documentation content is located in the `docs/` directory.

### Local Development

To preview the documentation locally, ensure you have Python installed and then:

1. Install dependencies:
   ```bash
   pip install mkdocs-material
   ```

2. Start the development server:
   ```bash
   mkdocs serve
   ```

3. Open `http://127.0.0.1:8000` in your browser.

### Deployment

Changes pushed to the `main` branch are automatically deployed to GitHub Pages via GitHub Actions.

## 📁 Structure

- `docs/`: Documentation source files (Markdown).
  - `en/`: English version.
  - `zh/`: Traditional Chinese version.
- `mkdocs.yml`: MkDocs configuration file.
- `.github/workflows/deploy.yml`: CI/CD deployment script.