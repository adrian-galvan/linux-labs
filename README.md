# linux-labs
Laboratorios prácticos de administración GNU/Linux sobre Debian: sistema, permisos, procesos, servicios, almacenamiento, SSH y troubleshooting.

🐧 Linux Labs

Laboratorios prácticos de administración GNU/Linux realizados sobre Debian GNU/Linux en VirtualBox, principalmente mediante TTY y shell sin interfaz gráfica.

Este repositorio documenta mi práctica durante mi formación en Administración de Redes Linux con orientación en Ciberseguridad, junto con ejercicios y escenarios propios de administración y troubleshooting.

🎯 Objetivo

El objetivo de estos laboratorios no es únicamente registrar comandos, sino documentar el proceso utilizado para administrar y diagnosticar un sistema Linux:

inspeccionar → entender → buscar evidencia → aplicar el cambio mínimo → verificar

Cada laboratorio incluye, cuando corresponde:

objetivo del escenario;
entorno utilizado;
estado inicial;
comandos ejecutados;
evidencia observada;
cambios realizados;
verificación del resultado;
errores encontrados y resolución.

### 01 — Debian GNU/Linux Installation on VirtualBox

Instalación de Debian GNU/Linux 13 mediante netinst sobre VirtualBox, configuración de la máquina virtual, particionado con LVM e instalación mínima sin entorno gráfico orientada al trabajo mediante TTY y shell.
Navegación por el sistema de archivos, archivos y directorios, rutas absolutas y relativas, herramientas GNU/Linux y uso de shell.

### 02 — Users, Groups & Permissions

Administración de usuarios y grupos, ownership, permisos clásicos, SGID, ACL y Sticky Bit sobre un directorio compartido, con verificación de herencia de grupo y resolución de `Permission denied`.

### 03 — Processes, Signals & Services

Administración y monitoreo de procesos mediante `ps`, `pstree` y `top`, identificación de PID/PPID y estados, uso de SIGTERM y SIGKILL, y administración de `ssh.service` mediante `systemctl`.

04 — Storage & Mounting

Dispositivos de bloque, particiones, sistemas de archivos, montaje, UUID y configuración mediante /etc/fstab.

05 — SSH & Troubleshooting

Configuración y prueba de OpenSSH, conexiones locales, análisis del servicio sshd, logs y diagnóstico de problemas.

06 — Pipes & Text Tools

Pipes, redirecciones y procesamiento de texto mediante herramientas como grep, cut, sort y otras utilidades GNU/Linux.

🛠️ Entorno
Debian GNU/Linux
Oracle VirtualBox
Bash / shell
TTY
systemd
OpenSSH
Vim
GNU coreutils
📚 Formación

Actualmente curso el Diplomado Administrador de Redes Linux con orientación en Ciberseguridad y Hacking Ético con Kali Linux en la Universidad Tecnológica Nacional — Facultad Regional Delta.

El repositorio se irá ampliando a medida que avance en administración Linux, redes, servicios, scripting y seguridad.

👤 Autor

Adrián Galván
Linux Administration Student · IT Support · Infrastructure

LinkedIn
