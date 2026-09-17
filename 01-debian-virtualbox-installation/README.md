# Lab 01 — Instalación de Debian GNU/Linux 13 en VirtualBox

[← Índice de laboratorios](../README.md) · [Perfil de Adrián Galván](https://github.com/adrian-galvan)

## Evidencia

[Ver el PDF con las capturas paso a paso de la instalación](images/FotosPasoaPasoVmOracle.pdf).

## Objetivo

Crear desde cero una máquina virtual destinada a mis laboratorios de administración GNU/Linux.

La instalación se realizó utilizando Debian GNU/Linux 13 "Trixie" mediante una imagen `netinst`, sobre Oracle VirtualBox ejecutado desde Windows 11.

Se optó por una instalación sin entorno gráfico para trabajar principalmente mediante TTY y shell.

---

## Entorno

- Host: Windows 11
- Hipervisor: Oracle VirtualBox
- Sistema invitado: Debian GNU/Linux 13 "Trixie"
- Arquitectura: amd64
- Instalador: netinst
- Memoria RAM: 12 288 MB (12 GiB)
- CPU virtuales: 6
- Firmware de la VM: EFI habilitado
- Disco virtual: 40 GiB, aproximadamente 43 GB decimales
- Gestión de almacenamiento: LVM
- Sistema de archivos: ext4
- Swap habilitada
- Interfaz principal: TTY / shell

---

## 1. Descarga de herramientas

Se descargaron las herramientas necesarias desde sus sitios oficiales:

- [Descarga oficial de Debian GNU/Linux](https://www.debian.org/download).
- [Descarga oficial de Oracle VirtualBox](https://www.virtualbox.org/wiki/Downloads).

La imagen utilizada fue:

`debian-13.6.0-amd64-netinst.iso`

Este nombre corresponde a la imagen utilizada en la práctica y visible en el PDF; las versiones ofrecidas en la página de descargas pueden cambiar.

---

## 2. Creación de la máquina virtual

Se creó una nueva máquina virtual en Oracle VirtualBox.

Durante la configuración se definieron:

- nombre de la VM;
- ubicación de almacenamiento;
- imagen ISO de Debian;
- cantidad de memoria RAM;
- cantidad de CPU;
- tamaño del disco virtual.

La virtualización por hardware debe encontrarse habilitada en BIOS/UEFI para poder utilizar correctamente VirtualBox.

---

## 3. Arranque desde la ISO

La imagen ISO de Debian fue seleccionada previamente en la configuración de la máquina virtual como medio de arranque.

Al iniciar la VM, VirtualBox arrancó desde la ISO y mostró el menú de instalación de Debian.

Para este laboratorio se seleccionó manualmente la opción:

`Install`

en lugar de `Graphical install`, para realizar la instalación mediante la interfaz de texto del instalador.

---

## 4. Configuración regional

Durante la instalación se configuraron:

- idioma: Español;
- ubicación: Argentina;
- distribución de teclado: Latinoamericano.

---

## 5. Configuración de red

Se configuró el hostname de la máquina para poder identificarla dentro de la red.

El nombre de dominio se dejó vacío, ya que este laboratorio se realiza en una red doméstica y no requiere un dominio configurado.

---

## 6. Usuarios

Durante la instalación se configuraron:

- contraseña para el usuario `root`;
- usuario normal: `adrian`;
- contraseña para el usuario normal.

La cuenta de usuario normal se utiliza para el trabajo cotidiano, mientras que `root` queda reservado para tareas administrativas.

---

## 7. Particionado y LVM

Se utilizó el método de particionado:

**Guiado — utilizar todo el disco y configurar LVM**

Dentro de LVM se crearon volúmenes lógicos separados para:

- `/`
- `/home`
- `/var`
- `/tmp`
- `swap`

También se creó la partición necesaria para el arranque UEFI.

Los sistemas de archivos principales fueron configurados utilizando `ext4`.

Para esta VM de laboratorio se utilizó prácticamente todo el espacio disponible del disco virtual.

---

## 8. Configuración de repositorios

Durante la instalación se habilitó el uso de una réplica de Debian para descargar paquetes desde Internet.

Se seleccionó:

- país: Argentina;
- réplica Debian disponible;
- conexión directa sin proxy HTTP.

---

## 9. Selección de software

No se instaló ningún entorno gráfico.

El objetivo fue disponer de un sistema Debian mínimo orientado al trabajo desde terminal.

Se mantuvieron las utilidades estándar del sistema.

También se seleccionó **SSH server**, como muestra la captura de selección de programas. Esto dejó disponible OpenSSH Server para las prácticas posteriores de administración de servicios.

---

## 10. Resultado

La instalación finalizó correctamente.

El sistema quedó disponible mediante terminal TTY y permite iniciar sesión tanto con el usuario normal como con `root`.

Esta máquina virtual constituye el entorno base de los siguientes laboratorios de administración GNU/Linux.

---

## Decisiones técnicas

Para este laboratorio se eligió:

- Debian por ser la distribución utilizada durante mi formación;
- instalación `netinst` para trabajar con una instalación mínima;
- VirtualBox para mantener un entorno aislado y reproducible;
- LVM para comenzar a trabajar con gestión flexible de almacenamiento;
- instalación sin entorno gráfico para priorizar el uso de shell y herramientas de administración.

---

## Continuidad de las prácticas

Esta VM se utiliza como base para practicar:

- navegación por el sistema de archivos;
- usuarios y grupos;
- permisos y ACL;
- procesos y señales;
- systemd y servicios;
- logs;
- almacenamiento y montaje;
- SSH;
- pipes y redirecciones;
- troubleshooting.

Ya están publicados los laboratorios de [usuarios, grupos y permisos](../02-users-groups-permissions-acl-stickybit/README.md), [procesos, señales y servicios](../03-processes-signals-services/README.md) y [APT, repositorios y confianza](../04-apt-repositories-package-trust/README.md). Los demás temas se irán incorporando al [índice general](../README.md).
