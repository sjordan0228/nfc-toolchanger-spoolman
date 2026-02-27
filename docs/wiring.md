# Wiring Guide

## PN532 DIP Switch Settings (I2C Mode)
## The DIP switch settings below were for my particular board. please verify your own

Set the DIP switches on the PN532 board as follows:
- Switch 1: **ON**
- Switch 2: **OFF**

## PN532 to ESP32-S3 Wiring

| PN532 Pin | ESP32-S3 Pin |
|-----------|--------------|
| VCC       | 3V3          |
| GND       | GND          |
| SDA       | GPIO1        |
| SCL       | GPIO0        |

> **Note:** The 3.3V pin on the ESP32-S3 DevKitC-1 is labeled **3V3**.
> Do NOT connect VCC to 5V — the PN532 runs fine on 3.3V.

## I2C Address

The PN532 should appear at address **0x24** on the I2C bus.
You can verify this in the ESPHome logs after flashing — look for:
```
Results from bus scan:
Found i2c device at address 0x24
```

## Notes

- Test with dupont wires before soldering
- One ESP32-S3 + PN532 per toolhead (4 total)
- All units use the same GPIO pins since each is a separate device
