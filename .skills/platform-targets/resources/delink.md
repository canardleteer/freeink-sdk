---
device: delink
device_flag: FREEINK_DEVICE_DELINK
sdk_profile: DE_LINK
sdk_header: libs/hardware/BoardConfig/include/BoardConfig.h
shared_binary_envs: [delink]
mcu_family: s3
board_package: esp32-s3-devkitc1-n16r8
psram_in_ini: false
psram_on_silicon: true
fb_in_psram: false
sdmmc: true
block_device_interface: true
width: 800
height: 480
fb_bytes: 48000
controllers: [SSD1677]
grayscale: 4-level
viewable_insets: {top: 9, right: 3, bottom: 3, left: 3}
touch: none
multitouch: false
has_home_key: false
frontlight: pwm
ui_scale: 1.0
ppi_note: null
usb_detect: none
shared_pads: {}
caps: [FRONTLIGHT]
---

# de-link

ESP32-S3. X4-class 800×480 SSD1677, 4-bit SDMMC, single-channel PWM
frontlight (GPIO5). No touch. Profile ships `NO_FLIP`; a board that mounts
the panel rotated sets `ROTATE_180` on the profile.

Own sample env (`delink`): `-DFREEINK_DEVICE_DELINK=1` and
`-DUSE_BLOCK_DEVICE_INTERFACE=1`. `FREEINK_SD_SDMMC` auto-enables. Silicon
has PSRAM; the sample does not set `BOARD_HAS_PSRAM`.
