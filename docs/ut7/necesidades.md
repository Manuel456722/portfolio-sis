# 7.2 Necesidades de software en entornos productivos

Elegir software para un entorno de trabajo real no es elegir "el mejor programa", sino el que **funciona de forma fiable, encaja con lo que ya hay y cumple los requisitos legales y económicos** de la organización.

## 1. Qué revisar antes de elegir

| Ámbito | Qué compruebo | Error que quiero evitar |
|---|---|---|
| Hardware | CPU, RAM, almacenamiento (capacidad y velocidad/IOPS), GPU si hay diseño o IA, red | Comprar justo con los requisitos mínimos, sin margen para crecer |
| Software y red | Sistema operativo compatible, versiones, dependencias, base de datos, si necesita Internet siempre o solo para activar/actualizar | Conflictos de versiones o formatos que no se entienden entre sí |
| Organización y coste | Número de usuarios, tipo de licencia (pago único o suscripción), coste total, formación, soporte y cumplimiento del RGPD | Fijarme solo en el precio de compra y olvidar los costes ocultos |

### Conceptos clave

- **Mínimos vs. recomendados:** los mínimos solo garantizan que el programa *arranca*. En producción planifico sobre los **recomendados más un margen** de crecimiento.
- **TCO (*Total Cost of Ownership*):** coste total = licencia + hardware + implantación + formación + soporte + mantenimiento.
- **RGPD:** si el software trata datos personales, debe permitir medidas de seguridad adecuadas al riesgo (art. 32: cifrado, seudonimización, copias que permitan restaurar…) y hay que vigilar si los datos salen del Espacio Económico Europeo (Capítulo V).

### Requisitos funcionales y no funcionales

| Tipo | Pregunta que responde | Ejemplos |
|---|---|---|
| Funcional | ¿**Qué** tiene que hacer? | Emitir facturas, controlar stock, gestionar tickets de soporte |
| No funcional | ¿**Cómo** debe comportarse? | Seguridad, usabilidad, rendimiento, disponibilidad, mantenibilidad, portabilidad |

(Clasificación basada en ISO/IEC/IEEE 29148 para requisitos e ISO/IEC 25010 para calidad del producto).

## 2. "Estable primero, rápido después"

Una caída después de una actualización cuesta tiempo y dinero, así que en producción se eligen **ramas de soporte largo**:

| Sistema | Canal estable | Soporte oficial |
|---|---|---|
| Ubuntu LTS (24.04, 26.04) | LTS cada 2 años | 5 años gratis; 10 con Ubuntu Pro; hasta 15 con Legacy Support |
| Debian Stable | Stable | ~5 años (≈3 de seguridad + ≈2 de Debian LTS) |
| Windows Enterprise LTSC | LTSC | 5 años (Enterprise LTSC) o 10 años (IoT Enterprise LTSC) |

*Datos de ciclo de vida tomados del temario, verificados en las fuentes oficiales en noviembre de 2026.*

### Flujo seguro para actualizar

```text
Desarrollo  ──►  Pruebas (staging)  ──►  Copia previa (backup/snapshot)  ──►  Producción
(cambios)        (validar parches)        (poder volver atrás)                (ventana de mantenimiento)
```

> [!CAUTION]
> Nunca aplico un parche directamente en producción. Primero lo pruebo en *staging* y hago **backup o snapshot**; si algo falla, puedo restaurar en minutos en lugar de reinstalar.

Además: **medir** (tiempo de respuesta, CPU, RAM, disco, usuarios simultáneos) y **prever fallos** (copias, RAID/réplicas, escalado vertical u horizontal).

## 3. Adecuación según tamaño y sector

| Perfil | Solución típica |
|---|---|
| Autónomo / microempresa | Software estándar en la nube (SaaS): poco coste inicial, sin servidores propios |
| Pyme | Sistemas modulares: facturación, almacén, CRM |
| Gran empresa / industria | ERP corporativo (SAP, Odoo Enterprise, Dynamics) o desarrollo a medida |
| Oficina y administración | Suite ofimática, correo, navegador, gestión documental |
| Comercio | TPV, stock en tiempo real, facturación electrónica |
| Industria | Planificación de producción, CAD/CAM, SCADA |
| Diseño y multimedia | Edición de imagen, vídeo y 3D (GPU y RAM altas) |
| Desarrollo e informática | IDE, Git, helpdesk, gestión de proyectos, máquinas virtuales y contenedores |

## 4. Procedimiento: ¿cumple mi equipo los requisitos?

**Caso práctico:** quiero saber si mi equipo puede ejecutar una aplicación que recomienda **4 núcleos, 8 GB de RAM y 20 GB libres** en un sistema de 64 bits.

### Linux

```bash
# LC_ALL=C fuerza las etiquetas en inglés para que grep funcione aunque el sistema esté en español
LC_ALL=C lscpu | grep -E "Architecture|Model name|CPU\(s\)"
free -h               # memoria RAM total y disponible
df -h                 # espacio libre por partición
cat /etc/os-release   # distribución y versión exacta
```

### Windows (PowerShell)

```powershell
$info = Get-ComputerInfo
$info | Select-Object OsName, OsVersion, CsTotalPhysicalMemory
$info.CsProcessors | Select-Object Name, NumberOfCores, NumberOfLogicalProcessors
Get-Volume | Select-Object DriveLetter, FileSystemLabel, SizeRemaining, Size
```

> [!NOTE]
> `CsTotalPhysicalMemory` y `SizeRemaining` se muestran en **bytes**. Para pasarlos a GB: `[math]::Round($info.CsTotalPhysicalMemory / 1GB, 1)`.

<!-- CAPTURA: añade aquí la salida de los comandos en tu equipo
![Requisitos de mi equipo](../img/captura-requisitos.png)
-->

### Comprobación: tabla de decisión

Relleno la tabla con los datos de mi equipo y comparo con los recomendados:

| Recurso | Recomendado | Mi equipo | ¿Cumple? |
|---|---|---|:---:|
| Arquitectura | x86_64 | *(rellenar)* | |
| Núcleos | 4 | *(rellenar)* | |
| RAM | 8 GB | *(rellenar)* | |
| Disco libre | 20 GB | *(rellenar)* | |
| Sistema operativo | Ubuntu 24.04 LTS / Windows 11 | *(rellenar)* | |

Si algún recurso queda justo, no basta con "cumplir": aplico la regla del margen de crecimiento.

