# Proyecto: portfolio técnico con Docsify y GitHub Pages

Esta página documenta **cómo he construido este mismo sitio**: instalación de las herramientas, estructura del proyecto, prueba en local y publicación automática en GitHub Pages con GitHub Actions. Si sigo estos pasos en un equipo nuevo, obtengo exactamente el mismo resultado.

## 1. Qué es Docsify

Docsify genera un sitio de documentación **directamente desde ficheros Markdown**, sin compilar páginas HTML: un único `index.html` carga Docsify desde una CDN y este convierte los `.md` en web al vuelo. Uso **Docsify v5**.

| Fichero | Para qué sirve |
|---|---|
| `docs/index.html` | Carga Docsify, el tema y los plugins; contiene la configuración `window.$docsify` |
| `docs/README.md` | Portada del sitio (quién soy, qué es el portfolio, estado de las unidades) |
| `docs/_sidebar.md` | Menú lateral con las páginas de la unidad |
| `docs/_coverpage.md` | Página de bienvenida (opcional) |
| `docs/.nojekyll` | Evita que GitHub Pages ignore los ficheros que empiezan por `_` |
| `docs/ut7/*.md` | Páginas de contenido de la UT7 |
| `docs/img/` | Imágenes, diagramas y capturas |
| `.github/workflows/docsify.yml` | Flujo de GitHub Actions que publica `./docs` en cada `push` a `main` |

Estructura completa del repositorio:

```text
portfolio-sis/
├── .github/
│   └── workflows/
│       └── docsify.yml
└── docs/
    ├── .nojekyll
    ├── index.html
    ├── README.md
    ├── _coverpage.md
    ├── _sidebar.md
    ├── _404.md
    ├── img/
    └── ut7/
```

## 2. Instalación en Windows

### Paso 1. Node.js y pnpm (en PowerShell)

```powershell
winget install -e --id OpenJS.NodeJS.LTS
winget install -e --id pnpm.pnpm
node -v
pnpm -v
pnpm setup
```

Después **cierro y vuelvo a abrir VS Code y todas las terminales** para que se cargue el nuevo `PATH`.

### Paso 2. Docsify (en CMD)

```bat
pnpm add -g docsify-cli
docsify init ./docs
docsify serve ./docs
```

> [!WARNING]
> **Docsify se instala y se ejecuta desde CMD, no desde PowerShell.** La política de ejecución por defecto de PowerShell (`Restricted`) bloquea los scripts `.ps1` de los paquetes globales; CMD, en cambio, ejecuta los lanzadores `.cmd`. El análisis completo de este error está en [7.6 Búsqueda de documentación](ut7/busqueda.md).

## 3. Configuración de `index.html`

La actividad pide tres cosas: `name`, `loadSidebar` y un tema visual.

```html
<!-- Tema visual: core de Docsify v5 + modo oscuro automático -->
<link rel="stylesheet" href="//cdn.jsdelivr.net/npm/docsify@5/dist/themes/core.min.css" />
<link rel="stylesheet" href="//cdn.jsdelivr.net/npm/docsify@5/dist/themes/addons/core-dark.min.css"
      media="(prefers-color-scheme: dark)" />

<script>
  window.$docsify = {
    name: 'Portfolio SIS',   // nombre que aparece en el menú
    loadSidebar: true,       // carga _sidebar.md
    coverpage: true,         // carga _coverpage.md
    subMaxLevel: 2,          // muestra los ## de cada página en el menú
    auto2top: true,          // vuelve arriba al cambiar de página
  };
</script>
<script src="//cdn.jsdelivr.net/npm/docsify@5"></script>
```

Plugins añadidos: **búsqueda** (`search`), **zoom de imágenes** (`zoom-image`), **botón de copiar código** (`docsify-copy-code`) y resaltado de sintaxis para `bash`, `powershell`, `batch` y `yaml`.

### Callouts: v5 frente a v4

| Docsify v5 (el que uso) | Docsify v4 |
|---|---|
| `> [!NOTE]` · `> [!TIP]` · `> [!IMPORTANT]` · `> [!WARNING]` · `> [!CAUTION]` | `?> texto` (aviso informativo) · `!> texto` (aviso importante) |

Así se ven en este sitio:

> [!NOTE]
> Información útil que conviene conocer.

> [!TIP]
> Consejo para hacerlo mejor o más rápido.

> [!WARNING]
> Algo que puede dar problemas si no se tiene en cuenta.

## 4. Flujo de trabajo local

```text
Editar Markdown ──► docsify serve ──► revisar en localhost:3000 ──► corregir ──► commit & push
```

1. Edito los `.md` en VS Code.
2. Arranco el servidor: `docsify serve ./docs` (o `docsify serve .` si estoy dentro de `docs`).
3. Abro `http://localhost:3000` y recorro **todas** las secciones del menú.
4. Reviso enlaces, tablas, bloques de código, imágenes y capturas.
5. Subo los cambios a `main` y compruebo la versión publicada. Como respaldo, puedo imprimir la página a PDF desde el navegador.

<!-- CAPTURA: el sitio funcionando en http://localhost:3000
![Portfolio en localhost:3000](../img/captura-localhost.png)
-->

## 5. Publicación con GitHub Actions

### Paso 1. Workflow

En la **raíz** del repositorio (al mismo nivel que `docs/`, no dentro) creo `.github/workflows/docsify.yml`:

```yaml
name: Deploy Docsify to GitHub Pages

on:
  push:
    branches:
      - main

permissions:
  contents: read
  pages: write
  id-token: write

concurrency:
  group: pages
  cancel-in-progress: false

jobs:
  deploy:
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Setup Pages
        uses: actions/configure-pages@v5

      - name: Upload artifact
        uses: actions/upload-pages-artifact@v5
        with:
          path: ./docs
          include-hidden-files: true

      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v5
```

| Bloque | Qué hace |
|---|---|
| `on.push.branches: main` | Se ejecuta en cada `push` a la rama `main` |
| `permissions` | Permite leer el repositorio y escribir en Pages con un token OIDC |
| `concurrency` | Evita dos despliegues a la vez sin cancelar el que está en curso |
| `upload-pages-artifact` → `path: ./docs` | Empaqueta solo la carpeta del sitio (cámbialo si tu carpeta tiene otro nombre) |
| `deploy-pages` | Publica el paquete y devuelve la URL final |

> [!WARNING]
> **Incidencia real en el primer despliegue.** Con la combinación `upload-pages-artifact@v3` + `deploy-pages@v5`, el paso *Deploy to GitHub Pages* falló con `No artifacts named "github-pages" were found for this workflow run`, aunque el artefacto sí se había subido. Lo resolví actualizando a **`upload-pages-artifact@v5`**, su versión actual. Como la v5 excluye por defecto los ficheros ocultos, añadí `include-hidden-files: true` para que `.nojekyll` también se publique.

### Paso 2. Activar GitHub Pages

*Settings → Pages → Build and deployment → Source:* **GitHub Actions**.

### Paso 3. Primer push

```bat
git init
git add .
git commit -m "Configurar despliegue automático de Docsify"
git branch -M main
git remote add origin https://github.com/Manuel456722/portfolio-sis.git
git push -u origin main
```

### Comprobación

- En la pestaña **Actions** aparece *Deploy Docsify to GitHub Pages* y termina en **verde**.
- El sitio queda en `https://manuel456722.github.io/portfolio-sis/`.
- Cada nuevo `git push` a `main` vuelve a publicarlo automáticamente.

> [!IMPORTANT]
> Si la portada carga pero **el menú lateral no aparece**, lo primero que compruebo es que exista `docs/.nojekyll`. Sin él, GitHub Pages ignora `_sidebar.md` y `_coverpage.md` por empezar por guion bajo.

## 6. Entrega

| Requisito | Dónde se cumple |
|---|---|
| Proyecto Docsify en GitHub | Repositorio `Manuel456722/portfolio-sis` |
| URL de GitHub Pages | `https://manuel456722.github.io/portfolio-sis/` |
| `index.html` con `name`, `loadSidebar` y tema | `docs/index.html` (tema *core* de Docsify v5) |
| `_sidebar.md` con el menú | UT7 y sus páginas en `docs/_sidebar.md` |
| `README.md` con quién soy, qué es y estado | `docs/README.md` |
| ≥ 2 páginas de UT7 con tabla, código y callout | Las 6 páginas de la UT7 y esta misma |
| Revisión en `localhost:3000` | Apartado 4 de esta página |
| Profesor como colaborador | *Settings → Collaborators → Add people* → `mario@iesdoctorbalmis.com` |

## Fuentes

- Docsify — *Quick start*, *Configuration*, *Themes* y *UI Kit (callouts)*
- docsify-cli — repositorio oficial `docsifyjs/docsify-cli`
- pnpm — *Installation*; Node.js — descarga oficial; Microsoft Learn — `winget`
- Microsoft Learn — `about_Execution_Policies`
- GitHub Docs — *Publicar con un flujo de trabajo personalizado de GitHub Actions*
- Markdown Guide — markdownguide.org
