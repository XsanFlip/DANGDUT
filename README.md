# D.A.N.G.D.U.T. Flipper Zero Version

<img width="500" height="500" alt="ChatGPT_Image_Jun_30__2026__02_38_40_PM-removebg-preview" src="https://github.com/user-attachments/assets/0027e0fd-a976-4046-8d7a-f0572f8eb5f9" />


## Deauthentication Attack Notification & Guard Detection Utility Toolkit
_Wireless Guardian in Your Pocket._
_Author: XsanLahci_

## What this is

This is a Flipper Zero application (.fap) that displays live deauth/disassoc
attack detection status on the Flipper's own screen, by reading status lines
sent over UART from a DANGDUT WiFi Devboard (ESP32-S2) sensor plugged into
the Flipper's GPIO/devboard connector.

This app is purely a passive display/monitor. It does not transmit, inject,
or control the WiFi radio in any way, it only renders text data already
produced by the sensor firmware (dangdut_esp32s2_flipper.ino).

## Folder structure

dangdut_flipper_app/
  application.fam     - app manifest (name, entry point, category)
  dangdut_app.c        - main app source (UART read + screen rendering)
  images/              - icon assets folder (add dangdut.png, 10x10 px, 1-bit)
  README.md            - this file

## Requirements

- Flipper Zero with official firmware (or compatible custom firmware
  that supports FAP external apps and furi_hal_serial)
- ufbt (micro Flipper Build Tool) installed on your computer
  Install: pip install ufbt
- DANGDUT WiFi Devboard (ESP32-S2) flashed with dangdut_esp32s2_flipper.ino,
  which sends status lines over Serial1 (GPIO17/18) toward the Flipper.

## Build & install steps

1. Place this folder anywhere on your computer, e.g.:
     ~/flipper-apps/dangdut_flipper_app/

2. Open a terminal inside this folder and run:
     ufbt

   This compiles dangdut_app.c into a .fap file under the dist/ folder.

3. Connect your Flipper Zero via USB, then run:
     ufbt launch

   This installs the .fap onto the Flipper and launches it directly, or you
   can manually copy the generated .fap file (from dist/) to:
     SD Card / apps / Tools / dangdut_app.fap
   using qFlipper's file manager, then launch it from
   Flipper Zero menu > Apps > Tools > D.A.N.G.D.U.T.

4. Plug in the DANGDUT WiFi Devboard (ESP32-S2 flashed with the companion
   .ino sketch) into the Flipper Zero's GPIO header, matching the standard
   WiFi Devboard pin orientation.

5. Launch the app on the Flipper. You should see:
     "Waiting for devboard..."   until the first UART line arrives, then:
     "SYSTEM SECURE"             in normal state, or
     "ATTACK DETECTED"           blinking red banner + red LED blink,
                                  plus target/sender MAC and RSSI info

## Icon (optional)

Flipper app icons are 10x10 px, 1-bit XBM-style PNGs. If you skip creating
images/dangdut.png, ufbt will use a placeholder icon, the app will still
build and run fine without a custom icon.

## Controls

  BACK button -> exit the app and return to the Flipper menu

## Protocol reference (must match the ESP32-S2 firmware)

  SAFE|<total_packets>|<packets_per_sec>|<channel>
  ATTACK|<total_packets>|<packets_per_sec>|<channel>|<target_mac>|<sender_mac>|<rssi>

Lines are newline-terminated, sent once per second at 115200 baud.
