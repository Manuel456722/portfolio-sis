# UT7 - Elaboración de documentación técnica

**Objetivo:** elaborar documentación valorando y utilizando aplicaciones informáticas de propósito general.

En esta unidad he trabajado las herramientas que uso a diario para documentar y comunicar el trabajo técnico: cómo elegir software con la licencia adecuada, cómo comprobar que encaja en un entorno real, cómo escribir documentación, cómo enviarla y compartirla y cómo encontrar información fiable.

## Mapa de la unidad

| Página | Criterio | Procedimiento que documenta |
|---|---|---|
| [7.1 Licencias de software](ut7/licencias.md) | a) | Averiguar la licencia de un programa antes de instalarlo |
| [7.2 Software en entornos productivos](ut7/necesidades.md) | b) | Comprobar si un equipo cumple los requisitos de una aplicación |
| [7.3 Documentación y ofimática](ut7/ofimatica.md) | c) | Escribir un manual en Markdown y convertirlo a Word/HTML/PDF |
| [7.4 Correo y mensajería](ut7/correo.md) | d) | Configurar un cliente de correo seguro y verificar sus puertos |
| [7.5 Transferencia de ficheros](ut7/transferencia.md) | e) | Enviar ficheros a un servidor con `scp`, `sftp` y FileZilla |
| [7.6 Búsqueda de documentación](ut7/busqueda.md) | f) | Resolver un error con un método de búsqueda en cuatro pasos |
| [Proyecto: este portfolio](ut7/portfolio-docsify.md) | c) y f) | Crear, probar y publicar este sitio con Docsify y GitHub Pages |

> [!TIP]
> Todas las páginas siguen la misma plantilla: **contexto → tabla de referencia → procedimiento con comandos → avisos → comprobación**. Mantener una estructura fija es una de las reglas de la buena documentación (consistencia).

## Plantilla que uso en cada página

```markdown
# Título del procedimiento

Breve contexto: qué problema resuelve y cuándo se usa.

## Conceptos clave
| Concepto | Explicación |
|---|---|

## Procedimiento
1. Paso con su comando en un bloque de código.

> [!WARNING]
> Error típico y cómo evitarlo.

## Comprobación
Cómo sé que ha funcionado.

## Fuentes
```
