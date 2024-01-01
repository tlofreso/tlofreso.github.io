---
draft: true
date: 2023-07-18
authors:
  - me
categories:
  - Home Automation
---

# Tasmota and Sonoff S31

## Flashing with esptool.py

```bash
python -m venv venv
source venv/bin/activate
pip install esptool

MacBook-Pro:~/Projects/esp$ esptool.py flash_id
esptool.py v4.3
Found 2 serial ports
Serial port /dev/cu.usbserial-A50285BI
Connecting....
Detecting chip type... Unsupported detection protocol, switching and trying again...
Connecting...
Detecting chip type... ESP8266
Chip is ESP8266EX
Features: WiFi
Crystal is 26MHz
MAC: e8:68:e7:f2:83:83
Uploading stub...
Running stub...
Stub running...
Manufacturer: 5e
Device: 4016
Detected flash size: 4MB
Hard resetting via RTS pin...

MacBook-Pro:~/Projects/esp$ esptool.py write_flash 0 tasmota.bin 
esptool.py v4.3
Found 2 serial ports
Serial port /dev/cu.usbserial-A50285BI
Connecting...
Detecting chip type... Unsupported detection protocol, switching and trying again...
Connecting...
Detecting chip type... ESP8266
Chip is ESP8266EX
Features: WiFi
Crystal is 26MHz
MAC: e8:68:e7:f2:83:83
Stub is already running. No upload is necessary.
Configuring flash size...
Flash will be erased from 0x00000000 to 0x0009efff...
Compressed 648176 bytes to 462185...
Wrote 648176 bytes (462185 compressed) at 0x00000000 in 40.6 seconds (effective 127.7 kbit/s)...
Hash of data verified.

Leaving...
Hard resetting via RTS pin...
```