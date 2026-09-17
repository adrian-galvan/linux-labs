# Lab 04 — APT, Repositories & Package Trust

## Objetivo

Comprender y documentar el funcionamiento de los repositorios APT en Debian 13, trabajando con:

- formato clásico `.list`
- formato moderno Deb822 `.sources`
- repositorios externos
- metadata firmada
- claves públicas OpenPGP
- almacenes de confianza de APT
- `/etc/apt/keyrings/`
- `Signed-By`
- migración de una fuente clásica a Deb822

El laboratorio parte de la configuración real del sistema, provoca intencionalmente un fallo de verificación al agregar un repositorio externo sin una clave asociada y posteriormente corrige la configuración mediante una clave pública específica y `Signed-By`.

Finalmente, el repositorio externo también es migrado al formato Deb822.

---

## Entorno

- Debian GNU/Linux 13 "Trixie"
- Arquitectura `amd64`
- Oracle VirtualBox
- Instalación mínima sin entorno gráfico
- Trabajo mediante TTY y shell
- Usuario `root`
- APT
- `vim`
- `wget`

---

# 1. Configuración inicial de APT

La instalación utilizaba inicialmente el formato clásico de configuración de repositorios mediante:

```text
/etc/apt/sources.list
```

Se inspeccionó su contenido con:

```bash
cat /etc/apt/sources.list
```

El formato clásico representa cada fuente en una sola línea.

Ejemplo:

```text
deb http://debian.unnoba.edu.ar/debian/ trixie main non-free-firmware
```

Posteriormente se actualizaron los índices y se ejecutó la herramienta de modernización de APT:

```bash
apt update && apt modernize-sources
```

APT detectó el archivo clásico y lo convirtió al formato moderno Deb822.

El archivo anterior quedó preservado como respaldo `.bak`.

![Modernización de sources.list](images/01-sources-list-modernization.png)

---

# 2. Repositorios oficiales en formato Deb822

Luego de la modernización, la configuración oficial de Debian quedó almacenada en:

```text
/etc/apt/sources.list.d/debian.sources
```

Se inspeccionó mediante:

```bash
cat /etc/apt/sources.list.d/debian.sources
```

El formato Deb822 utiliza campos estructurados del tipo:

```text
Nombre: valor
```

Por ejemplo:

```text
Types: deb deb-src
URIs: http://debian.unnoba.edu.ar/debian/
Suites: trixie
Components: main non-free-firmware
Signed-By: /usr/share/keyrings/debian-archive-keyring.gpg
```

La misma información que en el formato clásico se encontraba concentrada en una línea ahora aparece separada en campos explícitos.

![Repositorios oficiales en Deb822](images/02-debian-deb822-sources.png)

Una característica importante de APT es que puede utilizar archivos `.list` y `.sources` simultáneamente.

Por ese motivo, para el siguiente paso se agregó temporalmente un repositorio externo utilizando nuevamente el formato clásico.

---

# 3. Crear un repositorio externo en formato clásico

Se creó un archivo independiente para Google Chrome:

```bash
touch /etc/apt/sources.list.d/google-chrome.list
```

Luego se abrió con:

```bash
vim /etc/apt/sources.list.d/google-chrome.list
```

Inicialmente se agregó la siguiente definición:

```text
deb [arch=amd64] https://dl.google.com/linux/chrome/deb/ stable main
```

La línea puede interpretarse de la siguiente manera:

```text
deb
→ repositorio de paquetes binarios

arch=amd64
→ utilizar esta fuente para arquitectura amd64

https://dl.google.com/linux/chrome/deb/
→ ubicación del repositorio

stable
→ suite definida por Google

main
→ componente del repositorio
```

![Repositorio Google en formato clásico](images/03-google-repository-classic-list.png)

El archivo no contiene físicamente el repositorio.

Contiene la definición necesaria para que APT sepa:

```text
dónde se encuentra
+
cómo debe consultarlo
```

---

# 4. Formato clásico y formato Deb822

La misma fuente también podría representarse utilizando Deb822.

Formato clásico:

```text
deb [arch=amd64 signed-by=/etc/apt/keyrings/google-chrome.asc] https://dl.google.com/linux/chrome/deb/ stable main
```

Formato Deb822 equivalente:

```text
Types: deb
Architectures: amd64
URIs: https://dl.google.com/linux/chrome/deb/
Suites: stable
Components: main
Signed-By: /etc/apt/keyrings/google-chrome.asc
```

La diferencia se encuentra principalmente en cómo se representa la configuración.

```text
.list
→ formato clásico de una línea

.sources
→ formato Deb822 basado en campos Nombre: valor
```

Esto es independiente del modelo de confianza utilizado por APT.

Un archivo `.list` también puede utilizar `Signed-By`.

---

# 5. Intento de actualización sin clave asociada

La fuente de Google fue agregada inicialmente sin `Signed-By`.

Se ejecutó:

```bash
apt update
```

APT logró contactar el repositorio y descargar su archivo `InRelease`, pero no pudo verificar criptográficamente la metadata.

Entre los mensajes apareció:

```text
Missing key ... which is needed to verify signature
```

![Error Missing key](images/04-missing-key-error.png)

Esto permitió comprobar que el problema no era:

```text
DNS
red
HTTPS
servidor inaccesible
```

APT había alcanzado correctamente el repositorio.

El problema era criptográfico:

```text
Google publica metadata
        ↓
Google firma esa metadata
        ↓
APT descarga metadata + firma
        ↓
APT necesita una clave pública confiable
        ↓
sin esa clave no puede verificar la firma
```

## Regla mental

```text
clave privada
→ firma

clave pública
→ verifica

Signed-By
→ indica qué clave o keyring debe utilizar APT
  para verificar una fuente determinada
```

---

# 6. Inspección de los almacenes de claves

Antes de agregar la clave de Google se inspeccionó:

```bash
ls -l /etc/apt/keyrings/
```

El directorio existía pero se encontraba vacío.

![Directorio local de keyrings vacío](images/05-local-keyrings-empty.png)

`/etc/apt/keyrings/` es una ubicación destinada a claves administradas localmente que posteriormente pueden ser asociadas explícitamente a una fuente mediante `Signed-By`.

También se inspeccionó el modelo histórico de confianza global.

Primero:

```bash
ls -l /etc/apt/trusted.gpg
```

En esta instalación de Debian 13 el archivo no existía.

Luego:

```bash
ls -l /etc/apt/trusted.gpg.d/
```

Este directorio sí contenía distintos archivos de claves de Debian.
Conceptualmente:

```text
/etc/apt/trusted.gpg
→ keyring global histórico basado en un único archivo

/etc/apt/trusted.gpg.d/
→ múltiples archivos/keyrings
→ continúan formando parte del modelo de confianza global

/etc/apt/keyrings/
+
Signed-By
→ claves administradas localmente
→ asociadas explícitamente a fuentes determinadas
```

Guardar una clave dentro de `/etc/apt/keyrings/` por sí solo no hace que APT confíe automáticamente en ella.

La fuente debe referenciarla mediante `Signed-By`.

---

# 7. Descargar la clave pública de Google

La clave pública utilizada para verificar el repositorio fue descargada mediante:

```bash
wget -O /etc/apt/keyrings/google-chrome.asc https://dl.google.com/linux/linux_signing_key.pub
```

![Keyrings de confianza global](images/06-global-trust-keyrings.png)

La estructura general de este comando es:

```text
wget [opción] [archivo de destino] [URL de origen]
```

En este caso:

```text
wget
→ programa utilizado para descargar el recurso

-O
→ indica el nombre y ubicación exactos
  del archivo de salida

/etc/apt/keyrings/google-chrome.asc
→ destino local

https://dl.google.com/linux/linux_signing_key.pub
→ recurso remoto
```

`wget -O` crea el archivo de destino si no existe.

No fue necesario utilizar previamente `touch`.

---

## Error encontrado durante la descarga

En el primer intento se escribió incorrectamente la URL:

```text
linux_signing-key.pub
```

en lugar de:

```text
linux_signing_key.pub
```

El servidor respondió:

```text
404 Not Found
```

Este error permitió distinguir un problema de ruta de un problema de conectividad.

La secuencia observada fue:

```text
DNS resolvió dl.google.com
        ↓
se estableció conexión HTTPS
        ↓
Google recibió la petición
        ↓
el recurso solicitado no existía
        ↓
404 Not Found
```

Luego de corregir la URL:

```bash
wget -O /etc/apt/keyrings/google-chrome.asc https://dl.google.com/linux/linux_signing_key.pub
```

la descarga finalizó correctamente.

Se verificó mediante:

```bash
ls -l /etc/apt/keyrings/
```

apareciendo:

```text
google-chrome.asc
```

![Descarga de la clave pública](images/07-google-key-download.png)

---

# 8. Asociar la clave al repositorio mediante Signed-By

En este punto la clave ya existía en:

```text
/etc/apt/keyrings/google-chrome.asc
```

pero todavía era necesario indicarle a APT qué fuente debía utilizarla.

Se volvió a editar:

```bash
vim /etc/apt/sources.list.d/google-chrome.list
```

Configuración original:

```text
deb [arch=amd64] https://dl.google.com/linux/chrome/deb/ stable main
```

Configuración modificada:

```text
deb [arch=amd64 signed-by=/etc/apt/keyrings/google-chrome.asc] https://dl.google.com/linux/chrome/deb/ stable main
```

![Configuración Signed-By](images/08-signed-by-configuration.png)

Ahora la relación quedó explícita:

```text
/etc/apt/keyrings/google-chrome.asc
        ↓
signed-by=
        ↓
google-chrome.list
        ↓
APT utiliza esa clave para verificar
esa fuente específica
```

Esto reduce el alcance de confianza respecto de utilizar una clave dentro del conjunto global.

---

# 9. Verificación correcta mediante apt update

Se ejecutó nuevamente:

```bash
apt update
```

Esta vez APT pudo consultar correctamente el repositorio de Google.

El flujo fue:

```text
APT encuentra Google
        ↓
descarga InRelease
        ↓
encuentra Signed-By
        ↓
utiliza google-chrome.asc
        ↓
verifica la firma
        ↓
acepta la metadata
        ↓
descarga el índice de paquetes
```

El error `Missing key` desapareció.

Esto todavía no instala Google Chrome.

Solamente permite que APT confíe en la metadata del repositorio y pueda incorporar su índice de paquetes.

---

# 10. Migración final del repositorio de Google a Deb822

Para finalizar el laboratorio, el repositorio externo fue migrado desde el formato clásico:

```text
google-chrome.list
```

al formato moderno Deb822:

```text
google-chrome.sources
```

La configuración final quedó almacenada en:

```text
/etc/apt/sources.list.d/google-chrome.sources
```

con el siguiente contenido:

```text
Types: deb
Architectures: amd64
URIs: https://dl.google.com/linux/chrome/deb/
Suites: stable
Components: main
Signed-By: /etc/apt/keyrings/google-chrome.asc
```

Luego se verificó el contenido mediante:

```bash
cat /etc/apt/sources.list.d/google-chrome.sources
```

y los archivos activos mediante:

```bash
ls /etc/apt/sources.list.d/
```

La configuración final quedó:

```text
/etc/apt/sources.list.d/
├── debian.sources
└── google-chrome.sources
```

Se ejecutó nuevamente:

```bash
apt update
```

APT consultó correctamente los repositorios oficiales de Debian y el repositorio de Google sin errores de firma.

La misma captura permite comprobar:

```text
debian.sources + google-chrome.sources
        ↓
configuración Deb822 de Google
        ↓
Signed-By configurado
        ↓
apt update exitoso
```

![Configuración final en Deb822](images/09-google-repository-deb822-final.png)

---

# Resultado final

El laboratorio permitió diferenciar dos conceptos que pueden confundirse fácilmente.

## Formato de configuración

```text
.list
vs
.sources
```

determina cómo se describe una fuente de paquetes.

```text
.list
→ sintaxis clásica de una línea

.sources
→ sintaxis Deb822 estructurada por campos
```

## Modelo de confianza

```text
trusted.gpg / trusted.gpg.d
vs
/etc/apt/keyrings + Signed-By
```

determina cómo APT obtiene y limita las claves utilizadas para verificar repositorios.

Por lo tanto:

```text
.list ≠ necesariamente confianza antigua

.sources ≠ automáticamente confianza moderna
```

Un archivo `.list` puede utilizar perfectamente:

```text
signed-by=/etc/apt/keyrings/...
```

La sintaxis de la fuente y el modelo de confianza son conceptos relacionados, pero independientes.

---

# Flujo completo del laboratorio

```text
/etc/apt/sources.list
        ↓
apt modernize-sources
        ↓
debian.sources
        ↓
crear google-chrome.list
        ↓
agregar Google sin Signed-By
        ↓
apt update
        ↓
Missing key
        ↓
inspeccionar almacenes/keyrings
        ↓
/etc/apt/keyrings/
        ↓
descargar clave pública de Google
        ↓
google-chrome.asc
        ↓
agregar Signed-By
        ↓
apt update
        ↓
firma verificada
        ↓
índice aceptado
        ↓
migrar google-chrome.list
        ↓
google-chrome.sources
        ↓
apt update final
        ↓
configuración completamente funcional
```

---

# Comandos principales utilizados

```bash
cat /etc/apt/sources.list

apt update

apt modernize-sources

cat /etc/apt/sources.list.d/debian.sources

touch /etc/apt/sources.list.d/google-chrome.list

vim /etc/apt/sources.list.d/google-chrome.list

apt update

ls -l /etc/apt/keyrings/

ls -l /etc/apt/trusted.gpg

ls -l /etc/apt/trusted.gpg.d/

wget -O /etc/apt/keyrings/google-chrome.asc https://dl.google.com/linux/linux_signing_key.pub

ls -l /etc/apt/keyrings/

vim /etc/apt/sources.list.d/google-chrome.list

apt update

cat /etc/apt/sources.list.d/google-chrome.sources

ls /etc/apt/sources.list.d/

apt update
```

---

# Conclusión

Este laboratorio permitió estudiar APT más allá de los comandos habituales de instalación y actualización de paquetes.

Se trabajó con:

```text
fuentes de software
formatos .list y .sources
Deb822
repositorios externos
metadata firmada
claves públicas
keyrings
Signed-By
errores OpenPGP
errores HTTP
migración de configuración
verificación final
```

La práctica también permitió observar errores reales y diagnosticarlos en lugar de limitarse a seguir una secuencia de comandos.

El error `Missing key` mostró que alcanzar un repositorio no implica poder confiar en su metadata.

El error `404 Not Found` permitió distinguir una URL incorrecta de un problema de red.

Finalmente, la configuración quedó migrada al formato Deb822 y el repositorio externo de Google quedó asociado explícitamente a su clave pública mediante `Signed-By`.

---

## Estructura final

```text
/etc/apt/sources.list.d/
├── debian.sources
└── google-chrome.sources

/etc/apt/keyrings/
└── google-chrome.asc
```

La configuración final utiliza fuentes Deb822 y limita explícitamente la clave utilizada para verificar el repositorio externo.
