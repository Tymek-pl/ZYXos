# ZYXos

> A custom embedded operating system for the ESP32-WROVER, built from scratch in C using ESP-IDF.

---

## What is ZYXos?

ZYXos is a fully custom OS shell for the ESP32-WROVER microcontroller. It runs directly on the hardware with no Linux, no RTOS userspace — just a bare-metal shell on top of FreeRTOS and ESP-IDF. It features a Unix-inspired command-line interface accessible over USB serial, a RAM-based filesystem, WiFi management, hardware control, and a package manager called ZPR that lets you install and run programs over WiFi without ever reflashing the device.

**Hardware:** ESP32-WROVER (Dual Xtensa LX6 @ 240MHz, 520KB SRAM, 8MB PSRAM, WiFi)

---

## Features

- Interactive shell (`zyxsh`) over USB serial
- 50+ built-in commands across system, files, network, hardware, and tools
- In-RAM filesystem with file create/read/write/delete
- WiFi manager — connect, scan, ping, wget, DNS
- GPIO, ADC, PWM, LED control
- **ZPR** — ZYXos Package Repository, install programs over WiFi
- `run <program>` — execute `.zyx` scripts without reflashing
- ASCII art `fetch` command, `sysmon` CPU/RAM/temp monitor
- `matrix`, `morse`, `base64`, `sha256`, `calc` and more

---

## Installation

### Requirements

- ESP32-WROVER board
- USB cable (data, not charge-only)
- macOS / Linux with Python 3.x
- [ESP-IDF v5.3](https://docs.espressif.com/projects/esp-idf/en/v5.3/esp32/get-started/index.html)

### Option 1 — Flash prebuilt binary (easiest)

Download the latest `ZYXos.bin` from [Releases](https://github.com/Tymek-pl/ZYXos/releases) and flash it:

```bash
pip install esptool
esptool.py --chip esp32 -p /dev/tty.usbserial-XXXX -b 460800 \
  --before default_reset --after hard_reset write_flash \
  --flash_mode dio --flash_freq 40m --flash_size 2MB \
  0x1000 bootloader.bin \
  0x8000 partition-table.bin \
  0x10000 ZYXos.bin
```

Replace `/dev/tty.usbserial-XXXX` with your actual port (`ls /dev/tty.usbserial*`).

### Option 2 — Build from source

```bash
# 1. Install ESP-IDF v5.3
git clone --recursive https://github.com/espressif/esp-idf.git ~/esp/esp-idf
cd ~/esp/esp-idf && git checkout v5.3
./install.sh esp32
. ~/esp/esp-idf/export.sh

# 2. Clone ZYXos
git clone https://github.com/Tymek-pl/ZYXos.git
cd ZYXos

# 3. Build and flash
idf.py build
idf.py -p /dev/tty.usbserial-XXXX flash monitor
```

---

## Connecting to ZYXos

After flashing, connect via serial monitor at **115200 baud**:

```bash
# Using idf.py (recommended)
idf.py -p /dev/tty.usbserial-XXXX monitor

# Or using screen
screen /dev/tty.usbserial-XXXX 115200
```

You should see:

```
Witaj w ZYXos v1.0.0!
root@zyxos:/$
```

Press **Ctrl+]** to exit the monitor.

---

## Basic Usage

```
root@zyxos:/$ help          # show all commands
root@zyxos:/$ fetch         # system info
root@zyxos:/$ sysmon        # live CPU/RAM/temp monitor
root@zyxos:/$ ls            # list files
root@zyxos:/$ nano hello    # create/edit a file
root@zyxos:/$ cat hello     # read a file
```

### WiFi

```
root@zyxos:/$ scan                        # scan for networks
root@zyxos:/$ connect MyNetwork MyPass    # connect to WiFi
root@zyxos:/$ ping google.com             # test connection
root@zyxos:/$ wget http://example.com     # fetch URL
```

### ZPR Package Manager

```
root@zyxos:/$ zpr update          # fetch package index from GitHub
root@zyxos:/$ zpr list            # show available packages
root@zyxos:/$ zpr install hello   # download and install a package
root@zyxos:/$ run hello           # execute the installed program
root@zyxos:/$ zpr installed       # list installed packages
root@zyxos:/$ zpr remove hello    # uninstall a package
```

Programs are `.zyx` scripts downloaded from [ZPR](https://github.com/Tymek-pl/zpr) over WiFi. No reflashing needed.

### Hardware

```
root@zyxos:/$ gpio              # show GPIO status
root@zyxos:/$ gpio set 2 1      # set GPIO2 HIGH
root@zyxos:/$ gpio get 4        # read GPIO4
root@zyxos:/$ blink 5 200       # blink LED 5 times, 200ms interval
root@zyxos:/$ adc               # read ADC channels
root@zyxos:/$ pwm 5 1000 4096   # PWM on GPIO5, 1kHz, 50% duty
```

---

## Command Reference

| Category | Commands |
|---|---|
| System | `fetch`, `sysmon`, `uptime`, `temp`, `meminfo`, `cpuinfo`, `reboot`, `halt` |
| Files | `ls`, `cat`, `nano`, `touch`, `rm`, `cp`, `mv`, `grep`, `wc`, `df`, `stat` |
| Network | `scan`, `connect`, `disconnect`, `ifconfig`, `ping`, `wget`, `dns`, `ntp` |
| Hardware | `gpio`, `adc`, `blink`, `led`, `pwm`, `i2c` |
| ZPR | `zpr update/list/install/remove/info/installed`, `run` |
| Tools | `calc`, `timer`, `morse`, `base64`, `sha256`, `hex`, `ascii`, `matrix` |

---

## Project Structure

```
ZYXos/
├── CMakeLists.txt
├── sdkconfig.defaults
└── main/
    ├── main.c          # entry point
    ├── shell.c/h       # shell parser and dispatcher
    ├── fs.c/h          # in-RAM filesystem
    ├── wifi_mgr.c/h    # WiFi manager
    ├── cmd_system.c    # system commands
    ├── cmd_files.c     # file commands
    ├── cmd_net.c       # network commands
    ├── cmd_hw.c        # hardware commands
    ├── cmd_tools.c     # utility commands
    ├── cmd_zpr.c       # ZPR package manager + run
    └── zyx_common.h    # ANSI colors, macros
```

---

## ZPR — Package Repository

ZYXos has a built-in package manager that downloads programs from [github.com/Tymek-pl/zpr](https://github.com/Tymek-pl/zpr).

Packages are plain `.zyx` text scripts — one ZYXos shell command per line. They run on any ESP32-WROVER with ZYXos, with no compilation required.

To contribute a package, open a Pull Request to the ZPR repository.

---

## License

MIT License — free to use, modify and distribute.

---

## Links

- [ZPR Package Repository](https://github.com/Tymek-pl/zpr)
- [ESP-IDF Documentation](https://docs.espressif.com/projects/esp-idf/en/v5.3/esp32/)
- [Report an issue](https://github.com/Tymek-pl/ZYXos/issues)
