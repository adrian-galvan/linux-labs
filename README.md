# 🐧 Linux Labs

Laboratorios prácticos de administración **GNU/Linux sobre Debian 13 «Trixie» en VirtualBox**, realizados principalmente mediante TTY y shell sin entorno gráfico.

Este repositorio documenta mi práctica durante mi formación en Administración de Redes Linux con orientación en Ciberseguridad, junto con ejercicios y escenarios propios de administración y resolución de problemas (*troubleshooting*).

## 🎯 Objetivo

Documentar el proceso utilizado para administrar y diagnosticar un sistema Linux, desde la inspección inicial hasta la verificación del resultado:

**inspeccionar → entender → buscar evidencia → aplicar el cambio mínimo → verificar**

Cada laboratorio incluye, cuando corresponde:

- Objetivo del escenario y entorno utilizado.
- Estado inicial y comandos ejecutados.
- Evidencia observada y cambios realizados.
- Verificación del resultado.
- Errores encontrados y su resolución.

## 🧪 Laboratorios publicados

| N.º | Laboratorio | Contenido | Evidencia |
| --- | --- | --- | --- |
| 01 | [Debian GNU/Linux Installation on VirtualBox](01-debian-virtualbox-installation/README.md) | Instalación mediante `netinst`, configuración de la VM, LVM y sistema sin entorno gráfico. | [PDF con capturas](01-debian-virtualbox-installation/images/FotosPasoaPasoVmOracle.pdf) |
| 02 | [Users, Groups & Permissions](02-users-groups-permissions-acl-stickybit/README.md) | Usuarios, grupos, propiedad, permisos clásicos, SGID, ACL, Sticky Bit y diagnóstico de `Permission denied`. | 10 capturas |
| 03 | [Processes, Signals & Services](03-processes-signals-services/README.md) | `ps`, `pstree`, `top`, PID/PPID, jobs, SIGTERM/SIGKILL y administración de `ssh.service` con `systemctl`. | 8 capturas |
| 04 | [APT, Repositories & Package Trust](04-apt-repositories-package-trust/README.md) | Repositorios externos, `.list` y Deb822 `.sources`, claves públicas, `Signed-By` y resolución de un error de verificación de firma. | 9 capturas |

## 🗂️ Próximas áreas de documentación

Estos temas forman parte de la ampliación prevista del portafolio:

- **Storage & Mounting:** dispositivos de bloque, particiones, sistemas de archivos, montaje, UUID y `/etc/fstab`.
- **SSH & Troubleshooting:** conexiones, sesiones, logs y diagnóstico de fallos, ampliando la administración del servicio documentada en el Lab 03.
- **Pipes & Text Tools:** redirecciones, filtrado, herramientas de texto y combinación de comandos.
- **Fundamentos de shell y sistema de archivos:** navegación, archivos y directorios, rutas absolutas y relativas, y herramientas GNU/Linux.

## 🛠️ Entorno y herramientas

- Host: Windows 11.
- Hipervisor: Oracle VirtualBox.
- Invitado: Debian GNU/Linux 13 «Trixie», arquitectura `amd64`.
- Interfaz: TTY y Bash / shell, sin entorno gráfico.
- Administración: `systemd`, `systemctl`, OpenSSH y APT.
- Herramientas: Vim, GNU coreutils, `ps`, `pstree`, `top`, `setfacl`, `getfacl` y `wget`.

Las prácticas se realizan en una VM de laboratorio. Cada README especifica el entorno y las cuentas utilizadas.

## 📚 Formación

Actualmente curso el **Diplomado Administrador de Redes Linux con orientación en Ciberseguridad y Hacking Ético con Kali Linux** en la **Universidad Tecnológica Nacional — Facultad Regional Delta**.

Complemento esta formación con preparación para **LPIC-1 (101 y 102)**. El repositorio se irá ampliando a medida que avance en administración Linux, redes, servicios, scripting y seguridad.

## 👤 Autor

**Adrián Galván** — estudiante de Administración Linux, en transición profesional hacia Soporte IT e Infraestructura.

[Perfil de GitHub](https://github.com/adrian-galvan) · [LinkedIn](https://www.linkedin.com/in/adrian-galv%C3%A1n)
