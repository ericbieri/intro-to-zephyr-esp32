# Zehpyr Digikey Tutorial

## 1. Getting Started
### Workspace Structure

* [T3: Forest Topology](https://docs.zephyrproject.org/latest/develop/west/workspaces.html)
* [Zephyr RTOS](https://github.com/zephyrproject-rtos/zephyr)
* [Zephyr SDK](https://github.com/zephyrproject-rtos/sdk-ng)

### Start Docker Container
*  docker build -t env-zephyr-espressif -f ./Dockerfile.espressif .
*  docker run --rm -it -p 2222:22 -p 8800:8800 -v "${PWD}/workspace:/workspace" -w /workspace env-zephyr-espressif

### SSH into running container
* Click open remote Window (bottom left)
* In the Menu click *Connect to Host* -> *Add new SSH Host* 
	* Enter: root@localhost:2222 PW: zephyr
* Choose Workspace in VSCode: /zephyr.code-workspace (go to root dir first)
* Install recommended Extensions from Microsoft & Nordic Semi

### Start Devcontainer
* Reopen Workspace in Container

### Build code
* Change to project dir /workspace/apps/01_blink
* Set CONFIG_ESP32_USE_UNSUPPORTED_REVISION=y in prj.conf (Feather ESP32 uses an old V1.0 Core)
* Feather ESP32: west build -p always -b esp32_devkitc_wroom/esp32/procpu -- -DDTC_OVERLAY_FILE=boards/esp32_devkitc_wroom.overlay
* west build -p always -b esp32c3_devkitc -- -DDTC_OVERLAY_FILE=boards/esp32c3_devkitc.overlay

### Flash code
* Check python version: python --version
* Setup virtual env: python -m venv venv
* Activate venv (on linux/max): source venv/bin/activate
* Install pyserial & esptool: python -m pip install pyserial==3.5 esptool==4.8.1
* flash code to 0x1000: python -m esptool --port /dev/tty.usbserial-01D0FD1C --chip auto --baud 921600 --before default_reset --after hard_reset write_flash -u --flash_size detect 0x1000 ./workspace/apps/01_blink/build/zephyr/zephyr.bin
* 
* ESP32-C6: python -m esptool --port /dev/tty.usbserial-1410 --chip auto --baud 921600 --before default_reset --after hard_reset write_flash -u --flash_size detect 0x0 ./workspace/apps/01_blink/build/zephyr/zephyr.bin


## 7. Debugging with OpenOCD and GDB
### OpenOCD
#### Links
* [https://openocd.org/](https://openocd.org/)
* [OpenOCD - Doc](https://openocd.org/doc-release/html/index.html)
* [OpenOCD - Github](https://github.com/openocd-org/openocd)
* [OpenOCD - Espressif - Github](https://github.com/espressif/openocd-esp32)
  
#### Install
* Download latest [openocd-esp32](https://github.com/espressif/openocd-esp32/releases)
* unzip: tar -xzf openocd-esp32-*.tar.gz
* move to /Applications/openocd-esp32
* Add path to ~/.zshrc
  * export PATH=/Applications/openocd-esp32/bin:$PATH
  * export OPENOCD_SCRIPTS=/path/to/openocd-esp32/share/openocd/scripts
openocd -f board/esp32c6-builtin.cfg

#### Run (on host)
* openocd -s share/openocd/scripts/ -c 'set ESP_RTOS none; set ESP_FLASH_SIZE 0' -f board/esp32c6-builtin.cfg
* See also [openocd-esp32 on esp32-c6 (OCD-1053)](https://github.com/espressif/openocd-esp32/issues/350#issuecomment-2571331057)
* [OpenOCD config files](https://github.com/espressif/openocd-esp32/tree/master/tcl/board)

### GDB 
#### Links
* [GNU Project Debugger](https://sourceware.org/gdb/)
* [Quick Guide to GDB](https://beej.us/guide/bggdb/)
#### Run GDB (in devcontainer)
* /opt/toolchains/zephyr-sdk-0.16.8/riscv64-zephyr-elf/bin/riscv64-zephyr-elf-gdb build/zephyr/zephyr.elf
* target extended-remote host.docker.internal:3333 
