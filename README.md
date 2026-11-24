# Portfolio técnico · Sistemas Informáticos (DAM)

Sitio de documentación de la **UT7 - Elaboración de documentación técnica**, hecho con [Docsify v5](https://docsify.js.org) y publicado en GitHub Pages mediante GitHub Actions.

- **Sitio publicado:** https://manuel456722.github.io/portfolio-sis/
- **Contenido:** carpeta [`docs/`](docs/)
- **Despliegue:** [`.github/workflows/docsify.yml`](.github/workflows/docsify.yml) — cada `push` a `main` republica el sitio.

## Ver el sitio en local

En CMD (no en PowerShell):

```bat
pnpm add -g docsify-cli
docsify serve ./docs
```

Y abrir `http://localhost:3000`.
