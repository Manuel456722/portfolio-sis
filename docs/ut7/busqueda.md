# 7.6 Búsqueda y fiabilidad de documentación técnica

Buscar "a ojo" en Google suele llevar a soluciones antiguas, de otra versión o directamente peligrosas. Un **método sistemático** sobre fuentes oficiales ahorra tiempo y evita romper el sistema.

## 1. Flujo de búsqueda en cuatro pasos

```text
 1. Error exacto       2. Ayuda local        3. Docs oficiales      4. Verificar
 + versión y SO   ──►  (man / Get-Help) ──►  GitHub / IA       ──►  fecha, versión
                                                                    y seguridad
```

| Paso | Qué hago | Ejemplo |
|---|---|---|
| 1. Describir | Copio el mensaje de error **literal**, mejor en inglés | `cannot be loaded because running scripts is disabled on this system` |
| 2. Contextualizar | Añado programa, versión exacta y sistema operativo | `docsify-cli`, PowerShell 5.1, Windows 11 |
| 3. Priorizar fuentes | Manual local → documentación del fabricante → repositorio oficial | `Get-Help about_Execution_Policies`, Microsoft Learn |
| 4. Verificar | Compruebo fecha y versión y entiendo qué hace cada comando antes de ejecutarlo | ¿Cambiar la política afecta a todo el equipo o solo a mi usuario? |

## 2. Fuentes de referencia

### Ayuda integrada (sin Internet y exacta para mi versión)

```bash
# Linux
man scp              # manual completo
apropos compress     # busca páginas de manual por palabra clave
tar --help           # ayuda rápida del propio programa
```

```powershell
# Windows (PowerShell / CMD)
Get-Help Test-NetConnection -Examples   # ejemplos de uso
Get-Help Get-ComputerInfo -Online       # abre la versión web del manual
robocopy /?                             # ayuda de un ejecutable clásico
```

### Documentación oficial, GitHub y comunidades

| Tipo de fuente | Ejemplos | Qué vigilar |
|---|---|---|
| Oficial y estándares | Microsoft Learn, Ubuntu/Debian/Red Hat Docs, ArchWiki, MDN, RFC (ietf.org) | Que la versión documentada sea la mía |
| GitHub | `README.md`, pestaña *Wiki*, *Issues* cerrados | Que el issue esté resuelto y sea reciente |
| Foros técnicos | Stack Overflow, Super User | Votos, fecha y respuesta aceptada |
| Asistentes de IA | ChatGPT, Claude, Copilot, Gemini | **Pueden inventar** comandos, parámetros o rutas |

Búsquedas útiles dentro de GitHub:

```text
repo:docsifyjs/docsify is:issue is:closed "loadSidebar"
language:powershell sftp
```

> [!WARNING]
> Un asistente de IA es útil para explicar conceptos o resumir un manual, pero puede **alucinar**. Antes de ejecutar cualquier comando que me proponga, lo contrasto con `man`, `Get-Help` o la documentación oficial.

### Cómo formular una buena pregunta

Siempre incluyo cuatro datos: **qué intento hacer**, **sistema operativo y versión**, **comando o código que ejecuté** y **error completo**.

## 3. Operadores de búsqueda en la web

| Operador | Para qué sirve | Ejemplo |
|---|---|---|
| `"frase exacta"` | Coincidencia literal del error | `"Connection refused" port 22 ssh` |
| `site:` | Limitar a una web oficial | `site:learn.microsoft.com "Test-NetConnection"` |
| `-término` | Excluir ruido o versiones antiguas | `ubuntu ip estatica -18.04 -16.04` |
| `OR` | Cualquiera de dos términos (en mayúsculas) | `linux backup tar OR rsync` |
| `filetype:` | Filtrar por tipo de documento | `filetype:pdf "ISO 27001" guía` |
| `intitle:` | Exigir el término en el título | `intitle:SFTP FileZilla configuración` |
| `before:` / `after:` | Filtrar por fecha en Google | `docsify sidebar after:2025-01-01` |

> [!NOTE]
> Operadores como `since:`, `until:` o `filter:links` son de redes sociales (X/Twitter), no de los buscadores web. Y **Googlebot** no es un comando: es el robot que rastrea e indexa la web; yo consulto ese índice con palabras clave y operadores.

## 4. Caso práctico: error al instalar Docsify en PowerShell

**Problema:** al ejecutar `docsify` en PowerShell aparece un error del tipo *"no se puede cargar el archivo docsify.ps1 porque la ejecución de scripts está deshabilitada en este sistema"*.

1. **Error exacto + contexto:** busco `"running scripts is disabled on this system" docsify.ps1 Windows 11`.
2. **Ayuda local:**

   ```powershell
   Get-ExecutionPolicy -List
   Get-Help about_Execution_Policies
   ```

3. **Fuente oficial:** en Microsoft Learn (`site:learn.microsoft.com about_Execution_Policies`) confirmo que en Windows la política por defecto para clientes es `Restricted`, que bloquea los scripts `.ps1`.
4. **Verificación y decisión:** en lugar de relajar la seguridad de PowerShell, sigo la indicación del temario y **uso CMD**, que ejecuta los lanzadores `.cmd` que instala pnpm:

   ```bat
   docsify serve ./docs
   ```

> [!CAUTION]
> Muchas respuestas de foros proponen `Set-ExecutionPolicy Unrestricted` para todo el equipo. Es un **atajo inseguro**: si alguna vez hiciera falta cambiarla, lo mínimo sería limitarlo al usuario actual (`-Scope CurrentUser`) y con `RemoteSigned`, nunca `Unrestricted`.

## 5. Lista de verificación: ¿es fiable lo que he encontrado?

| ✅ Compruebo | ❌ Desconfío |
|---|---|
| **Autoría:** fabricante oficial o repositorio del proyecto | **Comandos sin explicar** qué modifican |
| **Fecha:** actualización reciente | **Páginas desconocidas** o clones sin contexto |
| **Versión:** coincide con mi SO y mi software | **Enlaces sospechosos:** acortadores, descargas externas |
| **Contraste:** otra fuente fiable dice lo mismo | **Atajos inseguros:** `chmod 777`, desactivar el cortafuegos, `curl … \| bash` sin leer |


