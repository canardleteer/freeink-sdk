---
device: murphy-m4
device_flag: FREEINK_DEVICE_MURPHY_M4
sdk_profile: MURPHY_M4
sdk_header: libs/hardware/BoardConfig/include/BoardConfig.h
shared_binary_envs: []
mcu_family: s3
board_package: esp32-s3-devkitc1-n16r8
psram_in_ini: false
psram_on_silicon: true
fb_in_psram: false
sdmmc: true
block_device_interface: false
width: 800
height: 480
fb_bytes: 48000
controllers: [SSD1677]
grayscale: 4-level
viewable_insets: {top: 9, right: 3, bottom: 3, left: 3}
touch: FT6336U
multitouch: false
has_home_key: false
frontlight: pwm-warm
ui_scale: 1.2
ppi_note: touch-board uiScale 1.2
usb_detect: none
shared_pads:
  13: i2c-sda
caps: [TOUCH, FRONTLIGHT, WARMLIGHT]
---

# Murphy M4

ESP32-S3R8. SSD1677 800×480 (GDEQ0426T82 landscape glass in a portrait
housing; software rotation later). FT6336U touch, dual warm/cool PWM
frontlight, 4-bit SDMMC. `FREEINK_SD_SDMMC` auto-enables. Display SCLK
shares GPIO4 with touch SCL; GPIO13 is touch SDA.

No uncommented sample env yet. `shared_binary_envs` is `[]`. `psram_in_ini`
and `block_device_interface` are false until a sample env sets them;
silicon has PSRAM. Distinct from Murphy M3.
