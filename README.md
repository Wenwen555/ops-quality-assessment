# Ops Quality Assessment

MkDocs technical documentation site for multimodal operator quality assessment.

## Local development

```bash
python -m pip install -r requirements-docs.txt
mkdocs serve -a 127.0.0.1:8000
```

## Build

```bash
mkdocs build --strict
```

GitHub Pages deployment is configured in `.github/workflows/deploy.yml`.
