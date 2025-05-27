# Thingino firmware para Personal Cam 2/Personal Cam Pan 

Este repositorio automatiza la creación de archivos ZIP para instalar el firmware de código abierto [Thingino](https://thingino.com/) en las cámaras SoC T31, específicamente **Personal Cam Pan** y **Personal Cam 2**. Está basado en el proyecto [cocus/t31-test-tar-upgrader](https://github.com/cocus/t31-test-tar-upgrader).

Cada vez que se publica una nueva versión en [themactep/thingino-firmware](https://github.com/themactep/thingino-firmware), este repositorio genera automáticamente dos archivos ZIP (`personalcam2-<etiqueta-de-versión>.zip` y `personalpan-<etiqueta-de-versión>.zip`) y los sube como nuevo release en este repositorio. 

La finalidad de este repo es eliminar la necesidad de ejecutar de forma manual en comando `make` en un sistema Linux, haciendo que el proceso de actualización sea más accesible para quienes no tengan el conocimiento de como hacerlo.

### ¿Cómo se usa?
**Nota**: Solo es compatible con las cámaras **Personal Cam Pan** y **Personal Cam2** (aunque en teoría podría funcionar con otros modelos - y no me hago responsable).

**Nota 2**: A veces el proceso se puede estancar, personalmente lo probé y funcionó bien en el 90% de los casos. Si la actualización falla, vas a tener que desarmar la cámara y seguir el tutorial de [Thingino Cloner](https://thingino.com/cloner) para recuperarla.




1. Visitá la [página de Releases](https://github.com/pocho-labs/thingino-firmware-personal/releases) de este repositorio.
2. Descargá el archivo ZIP más reciente para tu cámara:
   - `personalcam2-<etiqueta-de-versión>.zip` para Personal Cam2.
   - `personalpan-<etiqueta-de-versión>.zip` para Personal Cam Pan.
3. Descomprimí el archivo descargado en una tarjeta SD formateada en FAT32. **¡FAT16 o exFAT no van a funcionar!**
4. Insertá la tarjeta SD en la cámara, encendela y esperá.
5. Los LEDs de la cámara (amarillo, azul e IR) te van a indicar el estado de la actualización:
   - **Parpadeo azul, SIN IR, SIN amarillo**: Volcando respaldo a la SD.
   - **Parpadeo amarillo, IR ENCENDIDO, SIN azul**: No se encontró el archivo de actualización.
   - **Parpadeo azul, IR ENCENDIDO, SIN amarillo**: El tamaño esperado de la partición de arranque no coincide.
   - **Parpadeo azul + amarillo, SIN IR**: Proceso terminado, esperá a que el watchdog reinicie la cámara (o reiniciala vos).
   - **Parpadeo azul + amarillo, IR fijo**: No se encontró la partición de arranque.
   - **Azul fijo, SIN IR, SIN amarillo**: Flasheando u-boot.
   - **Amarillo fijo, SIN IR, SIN azul**: Generando respaldo completo de todas las particiones.
   - **Azul + amarillo fijo, SIN IR**: Borrando mtd1.
6. Una vez que los LEDs se apagan (o después de un ciclo de encendido manual), el sistema se reinicia con el nuevo u-boot, que va a flashear `autoupdate-full.bin`. Este paso no tiene indicación de LEDs, así que tené paciencia. Si pasa más de 5 minutos, hacé un ciclo de encendido (y cruzá los dedos).
7. Parpadeos rápidos del LED azul indican que el proceso terminó y la cámara está iniciando Thingino.
8. Si vas a flashear varias cámaras, eliminá todos los archivos de la sd y copiale de nuevo los archivos de adentro del zip, en el proceso de flasheado se backupean varias particiones y las guarda como archivos .bin, así que en el próximo flasheo va a intentar flashearlos y va a fallar el proceso **volviendo un ladrillo la segunda cámara**.


### ¿Cómo funciona?
Este repositorio usa [GitHub Actions](https://github.com/features/actions) para monitorear nuevas versiones en [themactep/thingino-firmware](https://github.com/themactep/thingino-firmware). Cuando se detecta una nueva versión, se ejecuta un flujo de trabajo que hace lo siguiente:
1. Clona el repositorio [cocus/t31-test-tar-upgrader](https://github.com/cocus/t31-test-tar-upgrader).
2. Ejecuta el comando `make` para generar `personalcam2.zip` y `personalpan.zip`.
3. Renombra los archivos ZIP para incluir la etiqueta de la versión de `thingino-firmware` (por ejemplo, `personalcam2-firmware-2025-05-20.zip`).
4. Crea una nueva versión en este repositorio con los archivos ZIP renombrados como activos, incluyendo las notas de la versión original de `thingino-firmware` y un enlace a la versión fuente.

La lógica de actualización está basada en `cocus/t31-test-tar-upgrader`, que aprovecha una funcionalidad en ciertos firmwares T31 SoC para ejecutar código desde una tarjeta SD, permitiendo flashear la cámara con el firmware Thingino. Para más detalles sobre el proceso técnico, mirá el [README original](https://github.com/cocus/t31-test-tar-upgrader/blob/main/README.md).

--------

## English

This repository automates the process of generating upgrade ZIP files for T31 SoC IP cameras, specifically for **Personal Cam Pan** and **Personal Cam2**, to install the open-source [Thingino firmware](https://thingino.com/). It builds upon the [cocus/t31-test-tar-upgrader](https://github.com/cocus/t31-test-tar-upgrader) project, which creates all-in-one upgrade packages for cameras with 16MB SPI flash. 

Whenever a new release is published in [themactep/thingino-firmware](https://github.com/themactep/thingino-firmware), this repository automatically generates two ZIP files (`personalcam2-<release-tag>.zip` and `personalpan-<release-tag>.zip`) and uploads them as release assets in this repository. 

This eliminates the need to manually run the `make` command on a Linux system, making the upgrade process more accessible.

### How to use it
**Note**: This only supports **Personal Cam Pan** and **Personal Cam2** cameras, but it might work with other models in theory.

**Note 2**: Sometimes the process might get stuck, but it has worked for me about 90% of the time. If the upgrade fails, you may need to disassemble the camera and follow the [Thingino Cloner](https://thingino.com/cloner) tutorial for recovery.

1. Visit the [Releases page](https://github.com/Pocho-Labs/thingino-firmware-personal/releases) of this repository.
2. Download the latest ZIP file for your camera:
   - `personalcam2-<release-tag>.zip` for Personal Cam2.
   - `personalpan-<release-tag>.zip` for Personal Cam Pan.
3. Unzip the downloaded file to a FAT32-formatted SD card. **FAT16 or exFAT will not work!**
4. Insert the SD card into the camera, power it on, and wait.
5. The camera’s LEDs (Yellow, Blue, and IR) will indicate the update status:
   - **Blue blink, NO IR, NO Yellow**: Dumping backup to SD.
   - **Yellow blink, IR ON, NO Blue**: Update file not found.
   - **Blue blink, IR ON, NO Yellow**: Expected boot partition size doesn’t match.
   - **Blue + Yellow blink, NO IR**: Process finished, wait for the watchdog to reboot (or reboot manually).
   - **Blue + Yellow blink, solid IR**: Couldn’t find boot partition.
   - **Blue solid, NO IR, NO Yellow**: Flashing u-boot.
   - **Yellow solid, NO IR, NO Blue**: Generating full backup of all partitions.
   - **Blue + Yellow solid, NO IR**: Erasing mtd1.
6. Once the LEDs go dark (or after a manual power cycle), the system reboots into the new u-boot, which will flash `autoupdate-full.bin`. This step has no LED indication, so be patient. If it takes more than 5 minutes, power cycle the camera.
7. Fast Blue LED blinks indicate the process is complete, and the camera is booting Thingino!
8. If you are going to flash multiple cameras, delete all files from the SD card and copy the files inside the zip to the SD card again. During the flashing process, several partitions are backed up and saved as .bin files. So, the next time you flash, it will try to flash them and the process will fail, **bricking the second camera**.


### How it works
This repository uses [GitHub Actions](https://github.com/features/actions) to monitor new releases in [themactep/thingino-firmware](https://github.com/themactep/thingino-firmware). When a new release is detected (via an IFTTT webhook), a workflow runs the following steps:
1. Clones the [cocus/t31-test-tar-upgrader](https://github.com/cocus/t31-test-tar-upgrader) repository.
2. Executes the `make` command to generate `personalcam2.zip` and `personalpan.zip`.
3. Renames the ZIP files to include the `thingino-firmware` release tag (e.g., `personalcam2-firmware-2025-05-20.zip`).
4. Creates a new release in this repository with the renamed ZIP files as assets, including the original `thingino-firmware` release notes and a link to the source release.

The underlying upgrade logic is based on `cocus/t31-test-tar-upgrader`, which exploits a feature in certain T31 SoC firmware to execute code from an SD card, allowing the camera to be flashed with Thingino firmware. For more details on the technical process, refer to the [original README](https://github.com/cocus/t31-test-tar-upgrader/blob/main/README.md).

