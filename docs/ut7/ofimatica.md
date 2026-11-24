# 7.3 Documentación técnica y herramientas ofimáticas

Lo que no se documenta hay que volver a investigarlo desde cero en cada incidencia o cada vez que cambia la persona responsable. Por eso documentar forma parte del trabajo técnico, no es un añadido. En esta página recojo los principios de una buena documentación, comparo las herramientas y documento cómo escribo un manual en Markdown y lo convierto a otros formatos.

## 1. Cinco reglas de una buena documentación

1. **Viva:** se actualiza con cada cambio de versión, ruta o comando.
2. **Organizada:** índice claro, jerarquía lógica, teoría separada de la práctica.
3. **Clara:** sin ambigüedades; marca qué pasos son obligatorios y qué comandos requieren permisos de administrador.
4. **Adaptada al lector:** no se escribe igual para un usuario final que para un técnico.
5. **Consistente:** misma terminología, formato de comandos y estilo en todo el documento.

### Tipos de documento que uso

| Documento | Para quién | Contenido |
|---|---|---|
| Manual de usuario | Usuario final | Guía paso a paso y preguntas frecuentes |
| Manual de administración | Técnico | Requisitos, despliegue, red, copias de seguridad y recuperación |
| README / wiki | Desarrolladores | Qué es el proyecto, estructura de carpetas, cómo arrancarlo en local |
| Changelog | Todos | Historial de novedades y correcciones entre versiones |
| Postmortem | Equipo técnico | Qué falló, causa raíz, solución aplicada y fecha |

## 2. Ofimática, texto plano o Markdown

| Enfoque | Cómo se trabaja | Punto fuerte | Lo elijo para… |
|---|---|---|---|
| Ofimática WYSIWYG | Edito directamente el aspecto final | Maquetación visual sencilla | Actas, presupuestos, informes corporativos |
| Texto plano (`.txt`) | Sin formato (Bloc de notas, Notepad++, nano) | Universal, ligero e inmediato | Notas rápidas, logs, ficheros `.conf` o `.csv` |
| Markdown (`.md`) | Texto plano con marcas sencillas (`#`, `*`, `` ` ``) que se convierte a web o PDF | Se versiona con Git y convive con el código | Manuales, wikis, README, **este portfolio** |

### Equivalencias entre suites

| Función | Microsoft 365 | LibreOffice (MPL 2.0) | Google Workspace | Formatos |
|---|---|---|---|---|
| Texto | Word | Writer | Documentos | `.odt`, `.docx`, `.pdf` |
| Hoja de cálculo | Excel | Calc | Hojas de cálculo | `.ods`, `.xlsx`, `.csv` |
| Presentaciones | PowerPoint | Impress | Presentaciones | `.odp`, `.pptx`, `.pdf` |
| Base de datos de escritorio | Access | Base | — | `.accdb`, `.odb` |

> [!TIP]
> Los dos formatos de oficina son estándares ISO: **ODF** (ISO/IEC 26300) y **OOXML** (ISO/IEC 29500). Aun así, un documento complejo puede verse distinto en Office y en LibreOffice. Para **entregar** un documento terminado lo exporto siempre a **PDF** (ISO 32000), que se ve igual en cualquier equipo.

Access o Base sirven para prototipos o bases pequeñas locales; para varios usuarios o aplicaciones web se usa un SGBD (PostgreSQL, MariaDB/MySQL, SQL Server).

## 3. Procedimiento: escribir un manual en Markdown y convertirlo

**Objetivo:** redactar un manual de instalación en Markdown (fácil de versionar) y generar a partir de él un `.docx` para quien lo necesite en Word y un `.html` independiente.

### Paso 1. Escribir el manual con la sintaxis esencial

````markdown
# Manual de instalación de Nginx

## Requisitos
- Ubuntu 24.04 LTS
- Usuario con permisos de `sudo`

## Instalación
1. Actualiza los repositorios e instala el paquete:

   ```bash
   sudo apt update && sudo apt install -y nginx
   ```

2. Comprueba que el servicio está activo.

## Puertos
| Parámetro | Valor | Descripción |
|---|---|---|
| Puerto | `443` | Tráfico HTTPS cifrado |

> Nota: abre el puerto en el cortafuegos si es necesario.
````

Resumen de marcas que más uso:

| Quiero… | Escribo |
|---|---|
| Título de nivel 1 / 2 / 3 | `#`, `##`, `###` |
| Negrita / cursiva | `**texto**` / `*texto*` |
| Código en línea | `` `comando` `` |
| Lista con viñetas / numerada | `- elemento` / `1. paso` |
| Enlace / imagen | `[texto](url)` / `![descripción](ruta.png)` |
| Cita o nota | `> texto` |

### Paso 2. Previsualizar en VS Code

| Atajo | Resultado |
|---|---|
| `Ctrl + Shift + V` | Vista previa en la pestaña actual |
| `Ctrl + K`, soltar, `V` | Vista previa lateral (pantalla dividida) |

### Paso 3. Convertir con Pandoc

```bash
# Markdown → Word
pandoc manual-instalacion.md -o manual-instalacion.docx

# Markdown → HTML completo e independiente (-s = standalone)
pandoc manual-instalacion.md -s -o manual-instalacion.html
```

> [!WARNING]
> Sin la opción `-s`, Pandoc genera solo un **fragmento** de HTML (sin `<html>`, `<head>` ni estilos). Si el fichero se va a abrir directamente en el navegador, hay que añadir `-s`.

<!-- CAPTURA: vista previa de VS Code y el .docx abierto en Writer/Word
![Vista previa del manual en VS Code](../img/captura-vscode-preview.png)
-->

### Comprobación

- El `.docx` abre en Word y en LibreOffice Writer con títulos, lista y tabla.
- El `.html` abre en el navegador y el bloque de código se ve con fuente monoespaciada.
- Para la entrega final, exporto a PDF desde Writer (*Archivo → Exportar como PDF*).

## Fuentes

- CommonMark Specification y GitHub Flavored Markdown Spec
- Visual Studio Code — *Markdown editing*
- Pandoc — *User's Guide*
- OASIS / ISO — ODF (ISO/IEC 26300), OOXML (ISO/IEC 29500), PDF (ISO 32000)
- Keep a Changelog 1.1.0
