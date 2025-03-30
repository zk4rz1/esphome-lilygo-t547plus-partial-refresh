<b>Original Project by Nickolay</b> (github://nickolay/esphome-lilygo-t547plus) slightly modified to <u>add display partial refresh</u>.

This repository contains a Display component for [ESPHome](https://esphome.io/)
to support the ESP32-S3 [LILYGO T5 4.7" Plus E-paper display](https://www.lilygo.cc/products/t5-4-7-inch-e-paper-v2-3).

(Do not confuse it with the original ESP32-based Lilygo T5 4.7 board).

For more info in the display components, see the [ESPHome display documentation](https://esphome.io/#display-components)

<b>This is a fork of [Nickolay Project](https://github.com/nickolay/esphome-lilygo-t547plus)</b> with the aim to add partial screen refresh. Since is not possibile to change to way how the display clears itself (blinking rapidly 3 times with total black/total white screen) I managed to reduce the "refresh area" to appear less "aggressive" (as perfectly described in a post linked below).
Any time a change happens, the color for each pixel is stored in a temporary variable.
When the color changes the screen is effectively refreshed **only in the changed area** (you should see small portion of screen blinking rapidly 3 times black/white).

## Usage

As instructed by Nickolay:
To use the board with [ESPHome](https://esphome.io/) **you have to put quite a
number of options in your esphome config**:
* Configure the aprpopriate board, variant, and framework versions in the [esp32 platform](https://esphome.io/components/esp32.html)
  This lines worked for me:
  ```yaml
  esp32:
  variant: esp32s3
  board: "esp32-s3-devkitc-1"
  framework:
    type: arduino
    version: 2.0.3
    platform_version: 5.1.1
  ```

* Set a bunch of `platformio_options`
  
  I still have some throubles (DARDUINO_RUNNING_CORE and DARDUINO_EVENT_RUNNING_CORE should have been changed), however, even if with some warnings, this work:
  ```yaml
    platformio_options:
        upload_speed: 921600
        monitor_speed: 115200
        board_build.mcu: esp32s3
        board_build.f_cpu: 240000000L
        board_build.arduino.memory_type: qspi_opi
        board_build.flash_size: 16MB
        board_build.flash_mode: qio
        board_build.flash_type: qspi
        board_build.psram_type: opi
        board_build.memory_type: qspi_opi
        board_build.boot_freq: 80m
        platform_packages:
          - "toolchain-riscv32-esp @8.4.0+2021r2-patch5"
        build_flags:
          - "-DBOARD_HAS_PSRAM"
          - "-DARDUINO_RUNNING_CORE=0"
          - "-DARDUINO_EVENT_RUNNING_CORE=0"
          - "-DARDUINO_USB_MODE=1"
          - "-DARDUINO_USB_CDC_ON_BOOT=1"
  ```
* Include the component from this repository as `external_components`

  ```yaml
  external_components:
  - source: github://zk4rz1/esphome-lilygo-t547plus-partial-refresh
    components: ["t547"]
  ```

If you clone this repository, a working example is included:

    git clone https://github.com/zk4rz1/esphome-lilygo-t547plus-partial-refresh.git
    cd esphome-lilygo-t547plus-partial-refresh
    esphome run basic.yaml

If you don't want to clone, copy the necessary pieces from [basic.yaml](./basic.yaml)
and adapt the `external_components` configuration as follows:

```yaml
# ... required esp32, platformio_options configuration omitted for brevity ...

external_components:
  - source: github://zk4rz1/esphome-lilygo-t547plus-partial-refresh
    components: ["t547"]

display:
- platform: t547
  id: t5_display
  update_interval: 10s
```

## Discussions

https://github.com/esphome/feature-requests/issues/1960
https://github.com/nickolay/esphome-lilygo-t547plus/issues/12
