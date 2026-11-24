# 7.5 Transferencia de ficheros

No existe un único "mejor" método para enviar un archivo: depende de **cuánto pesa**, de si emisor y receptor están **en la misma red**, de **cuánto tiempo** debe estar disponible y del **nivel de seguridad** que necesita. En esta página comparo los métodos y protocolos y documento cómo subir ficheros a un servidor por SSH.

## 1. Elegir el método según el escenario

| Método | Ideal para | Limitación | Ejemplos |
|---|---|---|---|
| Local directa | Archivos grandes en el mismo sitio o la misma LAN | Proximidad física o misma red/VPN | USB, Bluetooth, SMB v2/v3, NFS |
| Correo electrónico | Documentos pequeños y envíos formales | Límite de unos 20–35 MB por mensaje (25 MB en Gmail; 35 MB por defecto en Exchange Online) | Thunderbird, Outlook, Gmail |
| Nube con sincronización | Trabajo colaborativo, varios dispositivos | Depende de Internet y de terceros; vigilar permisos | Drive, OneDrive, Dropbox, Nextcloud |
| Envío temporal | Entregas grandes y puntuales | El enlace caduca a los pocos días | WeTransfer, SwissTransfer |
| Cliente-servidor (SFTP/SCP) | Servidores web, despliegues, automatización | Necesita credenciales y cliente o terminal | FileZilla, WinSCP, `sftp`, `scp` |

### ¿Por qué un PDF de 20 MB no cabe en un correo de 25 MB?

Los adjuntos se codifican en **Base64** (MIME, RFC 2045): cada 3 bytes pasan a ser 4 caracteres, es decir, el tamaño crece un tercio.

```text
20 MB × 4/3 ≈ 26,7 MB  (+ cabeceras y saltos de línea)  >  25 MB  → rechazado
```

## 2. Protocolos de transferencia

| Protocolo | Puerto | ¿Cifrado? | Cuándo usarlo |
|---|---|:---:|---|
| FTP (RFC 959) | 21 control / 20 datos (modo activo) | ❌ | Evitarlo: usuario, contraseña y datos viajan en claro |
| TFTP (RFC 1350) | 69/UDP | ❌ | Solo en una LAN aislada (arranque PXE, firmware); no tiene autenticación |
| FTPS (RFC 4217) | 21 (TLS explícito) / 990 (implícito, heredado) | ✅ TLS | FTP clásico protegido con certificados |
| **SFTP** | 22/TCP | ✅ SSH | **Opción recomendada**: un solo puerto y cifrado |
| SCP | 22/TCP | ✅ SSH | Copias rápidas no interactivas desde la terminal |
| HTTPS (RFC 9110) | 443 (TCP; UDP en HTTP/3) | ✅ TLS | Descargas web, repositorios y APIs |

> [!IMPORTANT]
> **FTP ≠ SFTP ≠ FTPS.** SFTP *no* es "FTP con S": es otro protocolo que funciona sobre SSH (puerto 22). FTPS sí es el FTP de siempre dentro de TLS. Además, desde **OpenSSH 9.0** el comando `scp` usa internamente SFTP; el protocolo antiguo solo se fuerza con `scp -O` para equipos viejos.

## 3. Procedimiento: subir y bajar ficheros de un servidor por SSH

**Escenario:** tengo que dejar mi informe en un servidor Linux de la red (`192.168.1.50`) y traerme una carpeta de documentación. Los comandos funcionan igual en Linux y en Windows 10/11 (OpenSSH viene incluido).

### Opción A: `scp` (copia directa)

```bash
# Subir un archivo a la carpeta documentos del usuario remoto
scp ./informe-ut7.pdf usuario@192.168.1.50:/home/usuario/documentos/

# Descargar una carpeta completa (-r = recursivo)
scp -r usuario@192.168.1.50:/var/www/docs ./copia-local/
```

### Opción B: `sftp` (sesión interactiva)

```bash
sftp usuario@192.168.1.50
```

| Comando dentro de `sftp` | Qué hace |
|---|---|
| `ls` / `lls` | Lista el directorio remoto / local |
| `cd` / `lcd` | Cambia de directorio remoto / local |
| `put fichero` | Sube un fichero |
| `get fichero` | Descarga un fichero |
| `bye` | Cierra la sesión |

### Opción C: cliente gráfico

- **FileZilla** (GPL v2+, multiplataforma): *Archivo → Gestor de sitios → Nuevo sitio*, protocolo **SFTP**, servidor `192.168.1.50`, puerto `22`. Doble panel local/remoto y cola de transferencias.
- **WinSCP** (GPL v3+, Windows): muy cómodo para SFTP y SCP.

<!-- CAPTURA: FileZilla conectado por SFTP con los dos paneles
![FileZilla conectado por SFTP](../img/captura-filezilla.png)
-->

> [!WARNING]
> La primera vez que conecto, SSH muestra la **huella (fingerprint)** del servidor y pregunta si confío en él. Debo comprobarla con el administrador: aceptarla a ciegas es justo lo que aprovecha un ataque de intermediario (*man-in-the-middle*).

### Comprobación

```bash
# Verifico en el servidor que el archivo ha llegado y pesa lo mismo
ssh usuario@192.168.1.50 "ls -lh /home/usuario/documentos/informe-ut7.pdf"
```

## 4. Carpetas compartidas en la red local

Rápidas y sin Internet, pero limitadas a la LAN o a una VPN.

```bat
:: Windows (SMB): conectar \\192.168.1.50\compartida como unidad Z:
net use Z: \\192.168.1.50\compartida /user:usuario
```

```bash
# Linux (NFS): montar un directorio exportado (requiere el paquete nfs-common)
sudo mount -t nfs 192.168.1.50:/srv/compartida /mnt/red
```

> [!NOTE]
> "CIFS" es el nombre del dialecto antiguo **SMB1**, obsoleto e inseguro. Hoy se usa SMB v2/v3.

## 5. Compartir con seguridad

- **Controla quién abre el enlace:** evita "cualquier persona con el enlace" en documentos internos.
- **Mínimo privilegio:** acceso solo a quien lo necesita, y en solo lectura si no tiene que editar.
- **Protege lo sensible:** contraseña y caducidad en el enlace; mejor aún, cifra el fichero antes (por ejemplo, `.7z` con AES-256) y envía la clave **por otro canal**.

```bash
# Comprimir y cifrar (también los nombres de archivo) con 7-Zip; pedirá la contraseña
7z a -p -mhe=on informe-confidencial.7z informe-ut7.pdf
```

## Fuentes

- IETF — RFC 959 (FTP), RFC 1350 (TFTP), RFC 4217 (FTPS), RFC 4251 (SSH), RFC 2045 (MIME/Base64), RFC 9110 (HTTP)
- OpenSSH — *Release notes 9.0*; manuales `scp(1)` y `sftp(1)`
- Ayuda de Gmail (límite de adjuntos) y Microsoft Learn (límites de Exchange Online)
- Microsoft Learn — `net use` y SMB
- FileZilla, WinSCP y 7-Zip — licencias y documentación oficial
