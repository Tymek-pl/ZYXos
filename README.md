## ZYXos v1.0.0 — First Stable Release

The first stable release of ZYXos — a custom embedded operating system for the ESP32-WROVER, built from scratch in C using ESP-IDF v5.3.

---

### What's included

**Shell**
- Interactive `zyxsh` shell over USB serial at 115200 baud
- Command history, backspace, ANSI colors
- 50+ built-in commands

**System commands**
`fetch` `sysmon` `uptime` `temp` `meminfo` `cpuinfo` `tasks` `dmesg` `reboot` `halt` `chip` `mac` `uname` `free` `ver`

**File commands**
`ls` `cat` `nano` `touch` `rm` `cp` `mv` `grep` `wc` `head` `tail` `df` `stat` `chmod`

**Network commands**
`connect` `disconnect` `scan` `ifconfig` `ping` `wget` `dns` `ntp` `ap`

**Hardware commands**
`gpio` `adc` `blink` `led` `pwm` `i2c` `gpioinfo`

**Tools**
`calc` `timer` `morse` `base64` `sha256` `hex` `ascii` `matrix` `banner` `echo` `rot13` `rev` `upper` `lower`

**ZPR Package Manager**
`zpr update` `zpr list` `zpr install` `zpr remove` `zpr info` `zpr installed` `run`

---

### Flashing

Download the files below and flash with esptool:

```bash
pip install esptool

esptool.py --chip esp32 -p /dev/tty.usbserial-XXXX -b 460800 \
  --before default_reset --after hard_reset write_flash \
  --flash_mode dio --flash_freq 40m --flash_size 2MB \
  0x1000  bootloader.bin \
  0x8000  partition-table.bin \
  0x10000 ZYXos.bin
```

Replace `/dev/tty.usbserial-XXXX` with your serial port.

---

### Quick start

```
root@zyxos:/$ connect YourSSID YourPassword
root@zyxos:/$ zpr update
root@zyxos:/$ zpr install hello
root@zyxos:/$ run hello
```

---

### Known limitations

- Filesystem is RAM-only — files are lost on reboot (fixed in v2.0.0)
- No OTA firmware updates (added in v2.0.0)
- WiFi credentials must be re-entered after reboot (fixed in v2.0.0)
- ZPR server must be running locally (HTTP only, no HTTPS due to ESP32 SSL limitations)

---

### Hardware tested on

- ESP32-WROVER (ESP32-D0WDQ6 revision v1.1)
- 8MB PSRAM
- ESP-IDF v5.3

---

### What's next — v2.0.0

- Persistent flash storage (LittleFS) — files survive reboot
- OTA firmware updates via `zpr sysupdate`
- Auto WiFi reconnect on boot
- Full `.zyx` scripting engine — variables, loops, conditions, user input
- 100% English interface
