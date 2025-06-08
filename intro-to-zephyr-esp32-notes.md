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

### Flash code
* Check python version: python --version
* Setup virtual env: python -m venv venv
* Activate venv (on linux/max): source venv/bin/activate
* Install pyserial & esptool: python -m pip install pyserial==3.5 esptool==4.8.1
* flash code to 0x1000: python -m esptool --port /dev/tty.usbserial-01D0FD1C --chip auto --baud 921600 --before default_reset --after hard_reset write_flash -u --flash_size detect 0x1000 ./workspace/apps/01_blink/build/zephyr/zephyr.bin
* 
* 




