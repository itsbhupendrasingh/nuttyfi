# NuttyFi 2.0 — Arduino Boards Package for ESP8266 (SME Dehradun)

Make your **NuttyFi 2.0** boards first‑class citizens in the Arduino IDE via Boards Manager.

> **TL;DR (Install in Arduino IDE 2.x)**
>
> 1. Open **File → Preferences → Additional Boards Manager URLs** and add:
>    `https://raw.githubusercontent.com/itsbhupendrasingh/Nuttyfi/main/package/package_nuttyfi_index.json`
> 2. **Tools → Board → Boards Manager…** → search **“NuttyFi”** → **Install**.
> 3. Select **Tools → Board → NuttyFi 2.0 (ESP‑12E/12F)** and you’re set.

---

## Quick links

* **Boards Manager URL:** `https://raw.githubusercontent.com/itsbhupendrasingh/Nuttyfi/main/package/package_nuttyfi_index.json`
* **Latest Release:** <ADD_GITHUB_RELEASE_LINK>
* **Issue Tracker (Support):** [https://github.com/itsbhupendrasingh/Nuttyfi/issues](https://github.com/itsbhupendrasingh/Nuttyfi/issues)

> **Note:** NuttyFi 2.0 is based on the ESP8266 toolchain and leverages Arduino‑ESP8266 under the hood, but this package provides a **clean, board‑focused install** with NuttyFi‑specific defaults, variants, and examples.

---

## What this package includes

* **NuttyFi 2.0 board definitions** (variants, uploader settings, flash layouts)
* **Pre‑tuned defaults** for stable Wi‑Fi, OTA, and filesystem (LittleFS) on NuttyFi 2.0
* **Curated examples** for typical NuttyFi use: Wi‑Fi AP+STA, OTA, HTTP server, MQTT, WebSocket, PWM/Servo, I²C/SPI buses, microSD (if fitted)
* **Pin aliases** that match the NuttyFi 2.0 silkscreen and documentation

If you need the upstream ESP8266 platform docs, see: [https://arduino-esp8266.readthedocs.io/](https://arduino-esp8266.readthedocs.io/) (used here as the underlying core).

---

## Supported boards

* **NuttyFi 2.0 (ESP‑12E/ESP‑12F)** — *default* variant: `nuttyfi_2`

---

## Pinout (NuttyFi 2.0 default)

> **Verify & edit** these to match your exact NuttyFi 2.0 schematic/silkscreen before publishing.

| Label | ESP8266 GPIO | Notes                                            |
| ----: | :----------: | ------------------------------------------------ |
|    D0 |    GPIO16    | Deep‑sleep wake; no PWM/I²C; boot strapping safe |
|    D1 |     GPIO5    | I²C SCL (recommended)                            |
|    D2 |     GPIO4    | I²C SDA (recommended)                            |
|    D3 |     GPIO0    | **BOOT strap**; keep HIGH at boot                |
|    D4 |     GPIO2    | Built‑in LED on some modules; **BOOT strap**     |
|    D5 |    GPIO14    | SPI SCK                                          |
|    D6 |    GPIO12    | SPI MISO                                         |
|    D7 |    GPIO13    | SPI MOSI                                         |
|    D8 |    GPIO15    | **BOOT strap**; must be LOW at boot (pull‑down)  |
|    RX |  GPIO3 (RX)  | UART0 RX                                         |
|    TX |  GPIO1 (TX)  | UART0 TX                                         |
|    A0 |     ADC0     | 0–1.0V (or 0–3.3V if board includes divider)     |

> Boot‑strapping pins **GPIO0, GPIO2, GPIO15** affect boot mode—avoid pulling them to the wrong levels at reset.

---

## Flash layouts

Default flash layout uses **4M (1M SPIFFS/LittleFS)**. You can choose others in **Tools → Flash Size**:

* **4M (1M LittleFS)** *(default)*
* **4M (2M LittleFS)**
* **1M (64K LittleFS)** *(for minimal builds)*

Adjust these in `boards.txt` if your production design differs.

---

## Getting started

1. **Install** the Boards package (see TL;DR above).
2. Open **File → Examples → NuttyFi 2.0** and choose **01_Basics/01_Blink** to verify your toolchain & upload.
3. Try **04_Network/HTTPServer_Basic** and connect to the printed IP address.

### Minimal blink example

```cpp
#include <Arduino.h>

#ifndef LED_BUILTIN
#define LED_BUILTIN D4 // adjust if your LED is on GPIO2
#endif

void setup(){
  pinMode(LED_BUILTIN, OUTPUT);
}

void loop(){
  digitalWrite(LED_BUILTIN, !digitalRead(LED_BUILTIN));
  delay(500);
}
```

---

## Filesystem (LittleFS)

* Use **LittleFS** for storing web assets, configs, and logs.
* Install the **LittleFS Data Upload** tool (ESP8266 LittleFS plugin) if you need to upload a `/data` folder.
* Example: **05_FS/LittleFS_WebUI** shows serving a simple web UI from flash.

> If you ship a custom uploader or pre‑compile assets, document it here.

---

## OTA (Over‑the‑Air updates)

* Enable **Basic OTA** example: **06_OTA/BasicOTA**.
* After first serial upload, subsequent uploads can be done over the network from **Port: network‑xxxx** in the IDE.
* For production, set an OTA password (see example).

---

## Wi‑Fi modes (STA/AP)

* **Station (STA)**: connect to existing Wi‑Fi.
* **Access Point (AP)**: NuttyFi creates its own SSID (default **IoT Gateway** or your brand — update the example).
* **AP+STA**: both at once; useful for provisioning.

Examples under **04_Network/** demonstrate all three modes.

---

## Upload settings (recommended)

* **Upload speed:** 921600 (fallback 115200 on flaky cables)
* **Flash mode:** DIO
* **Flash freq:** 40 MHz
* **CPU freq:** 80 MHz (use 160 MHz only if you need the extra performance)

These defaults are set in `boards.txt` for the NuttyFi 2.0 variant; adjust to match your hardware validation.

---

## Troubleshooting

* **Board not detected / timeout**: try lower upload speed, press reset during sync, or use a known‑good USB‑UART.
* **Boot loops**: check boot straps (GPIO0, GPIO2, GPIO15), power supply stability, and EN/RESET lines.
* **Wi‑Fi unstable**: use a solid 3.3V regulator, short ground returns, and add bulk/tantalum + 0.1µF decoupling.
* **LittleFS errors**: ensure flash layout matches what you selected under **Tools → Flash Size**.

Create new issues with logs and exact steps here: [https://github.com/itsbhupendrasingh/Nuttyfi/issues](https://github.com/itsbhupendrasingh/Nuttyfi/issues)

---

## Directory layout (for maintainers)

```
package/
  package_nuttyfi_index.json   # Boards Manager index (edit version, url, size, checksum)
platforms/
  esp8266/
    2.0.0/
      boards.txt
      platform.txt
      variants/
        nuttyfi_2/
          pins_arduino.h
examples/
  01_Basics/...
  04_Network/...
  05_FS/...
  06_OTA/...
```

* Each release ships a **tar.bz2** archive for the `platforms/esp8266/<version>/` content.
* Update **`url`**, **`archiveFileName`**, **`size`**, **`checksum`** (SHA‑256) in `package/package_nuttyfi_index.json` for every release.

---

## Versioning & releases

* Use semantic tags like **2.0.0**, **2.0.1**, etc.
* Attach the `nuttyfi-esp8266-<ver>.tar.bz2` archive to the corresponding GitHub Release.
* Document changes in **CHANGELOG.md** (add breaking changes, new pins, flash layouts, etc.).

---

## Contributing

PRs and issues are welcome. Please include:

* **NuttyFi package version** (Boards Manager)
* **Board model/variant**
* **Complete sketch** (minimal repro)
* **Serial logs** (for runtime issues) or **Verbose compile output** (for build issues)
* **Upload settings** (flash size/mode, CPU freq)

---

## License

* **Package & variant files:** <CHOOSE: MIT/BSD‑3/Apache‑2.0> (add LICENSE file)
* **Upstream Arduino‑ESP8266 core:** licensed per its repository; see their LICENSE.

---

## Credits

* **Schematics Microelectronics (SME Dehradun)** — NuttyFi hardware & Arduino package
* **ESP8266 Arduino Core maintainers** — underlying platform & toolchain

---

### Checklist before publishing

* [ ] Verify **pinout table** matches NuttyFi 2.0 PCB/silkscreen
* [ ] Confirm **LED_BUILTIN** mapping in `pins_arduino.h`
* [ ] Validate **default flash layout** in `boards.txt`
* [ ] Build & upload **examples** on real hardware
* [ ] Create **Release** with the platform tarball + update JSON (`size`, `checksum`)
* [ ] Fill in placeholders: release link, license, docs links, OTA defaults
