---
device: lilygo
device_flag: FREEINK_DEVICE_LILYGO
sdk_profile: LILYGO_T5S3
sdk_header: libs/hardware/BoardConfig/include/BoardConfig.h
shared_binary_envs: []
mcu_family: s3
board_package: esp32-s3-devkitc1-n16r8
psram_in_ini: false
psram_on_silicon: true
fb_in_psram: false
sdmmc: false
block_device_interface: false
width: 960
height: 540
fb_bytes: 64800
controllers: [LgfxEpd]
grayscale: 16-gray
viewable_insets: {top: 9, right: 8, bottom: 3, left: 8}
touch: GT911
multitouch: true
has_home_key: true
frontlight: pwm
ui_scale: 1.2
ppi_note: ~234 PPI comment in BoardConfig; uiScale is hand-tuned
usb_detect: none
shared_pads:
  13: sd-mosi
caps: [TOUCH, FRONTLIGHT, RTC]
---

# LilyGo T5 S3

ESP32-S3. 960×540 16-gray ED047TC1 raw-parallel panel via LovyanGFX
(`LgfxEpdDriver` / `FREEINK_DRIVER_LGFX_EPD`). GT911 with capacitive home
key (`LILYGO_T5_PRO_GT911`), PWM backlight, BQ27220/BQ25896 gauge, PCF8563
RTC. Bezel insets 9/8/3/8 (measured). `uiScale` 1.2. Power latch GPIO2.

The sample `[env:lilygo_t5s3]` is **commented out**, so
`shared_binary_envs` is `[]`. `psram_in_ini` is false until that env is
uncommented; silicon has PSRAM. A consumer still needs
`-DFREEINK_LGFX_EPD_CONFIG=lilygoT5S3LgfxConfig` and M5GFX on that env.
See `docs/lilygo-t5s3-support.md`.
