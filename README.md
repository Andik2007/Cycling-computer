# Cycling-computer
ESP32-S3 Gravel Bike Computer — an autonomous GPS bike computer.Stack: ESP32-S3, ILI9341+XPT2046, SD, GPS, AHT20.Features: Strava-compatible GPX recording, offline GeoJSON navigation with surface type color-coding, real-time telemetry, Wi-Fi web server for map/track synchronization, and power-saving mode.
# ESP32-S3 Gravel Bike Computer

A standalone GPS navigator and bike computer for gravel bikes based on the ESP32-S3 microcontroller. The device supports offline navigation, Strava-compatible track recording, ride metrics collection, and features a built-in web interface for wireless file management.

## 🚀 Features

- **Strava-ready GPX:** Records tracks with accurate timestamps for valid export to Strava.
- **Offline Maps:** Parsing and rendering of routes (GeoJSON/GPX) with color-coded surface indication (asphalt/gravel).
- **Wi-Fi Web Server:** Access point for uploading routes and downloading recorded tracks directly from a smartphone without removing the SD card.
- **Accurate Telemetry:** Displays speed, distance, battery level (with hardware calibration), and microclimate (AHT20).
- **Smart UI:** Automatic day/night theme switching based on satellite time.
- **Power Efficiency:** Transitions to `light_sleep` mode with hardware wake-up (interrupt) via touchscreen tap.

## 🛠 Hardware Stack

- **MCU:** ESP32-S3
- **Display:** 2.4" TFT ILI9341 (SPI)
- **Touchscreen:** XPT2046
- **Storage:** MicroSD module (HSPI)
- **Sensors:** GPS receiver (UART), Temperature/Humidity sensor AHT20 (I2C)
- **Power:** Li-Ion battery + resistive voltage divider (2x 100kΩ)

## 🔌 Wiring Diagram (Pinout)

| Component | ESP32-S3 Pins | Description |
| :--- | :--- | :--- |
| **ILI9341** | CS: 10, DC: 9, RST: 8 | SPI Bus: SCK (12), MISO (13), MOSI (11) |
| **XPT2046** | CS: 2, IRQ: 7 | Connected to the shared display SPI bus |
| **MicroSD** | CS: 15 | HSPI Bus: SCK (18), MISO (19), MOSI (21) |
| **GPS** | RX: 17, TX: 16 | UART (Serial1, 9600 baud) |
| **AHT20** | SDA: 4, SCL: 5 | I2C Bus |
| **Battery** | ADC: 1 | Voltage divider (100k/100k) |
| **Control** | 14, 6 | Peripherals power and backlight control |

## 📦 Dependencies (PlatformIO)

To compile the project, the following libraries are required:
- `Adafruit GFX Library`
- `Adafruit ILI9341`
- `Adafruit AHTX0`
- `XPT2046_Touchscreen`
- `TinyGPSPlus`

## ⚙️ Installation and Usage

1. Compile and upload the firmware to the ESP32-S3 via PlatformIO.
2. Format a MicroSD card to FAT32 and insert it into the module.
3. Turn on the device. The ESP32 will start a Wi-Fi access point:
   - **SSID:** `ESP32-Gravel`
   - **Password:** `12345678`
4. Connect to the network from your smartphone and open `http://192.168.4.1` in your browser.
5. Use the Web interface to upload your routes (`.json`, `.geojson`, `.gpx`) and download recorded rides (`ride.gpx`).

### 🔋 Battery Calibration
If the battery readings do not match your multimeter, adjust the multiplier in the `g_bat()` function (currently set to `2.065` to compensate for voltage drops on the divider):
```cpp
float v = (analogReadMilliVolts(b_p) / 1000.0) * 2.065;

=======================================================================
                         MCU: ESP32-S3 (Core)
=======================================================================

[ SPI Bus (Display & Touchscreen) ]
Pin 12  <======>  SCK (Shared)
Pin 13  <======>  MISO (Shared)
Pin 11  <======>  MOSI (Shared)
 |
 |-- [ ILI9341 (Display) ]
 |    Pin 10  <======>  CS
 |    Pin 9   <======>  DC
 |    Pin 8   <======>  RST
 |    Pin 6   <======>  LED (Display backlight control)
 |
 |-- [ XPT2046 (Touchscreen) ]
      Pin 2   <======>  CS
      Pin 7   <======>  IRQ (Hardware interrupt on touch)

[ HSPI Bus (MicroSD Module) ]
Pin 18  <======>  SCK
Pin 19  <======>  MISO
Pin 21  <======>  MOSI
Pin 15  <======>  CS

[ UART 1 Bus (GPS Module) ]
Pin 17  <======>  RX (Connect to GPS TX)
Pin 16  <======>  TX (Connect to GPS RX)

[ I2C Bus (AHT20 Climate Sensor) ]
Pin 4   <======>  SDA
Pin 5   <======>  SCL

[ Battery Level Measurement ]
Pin 1   <======>  ADC_1 (Connected to center of 100k/100k voltage divider)

[ SPI Initialization ]
Pin 14  <======>  CS pin (Used for custom 0x80 transmission during setup)
=======================================================================
