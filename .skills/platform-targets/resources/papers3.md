---
device: papers3
device_flag: FREEINK_DEVICE_PAPERS3
sdk_profile: M5PAPER_S3
sdk_header: libs/hardware/BoardConfig/include/BoardConfig.h
shared_binary_envs: [papers3]
mcu_family: s3
board_package: esp32-s3-devkitc1-n16r8
psram_in_ini: true
psram_on_silicon: true
fb_in_psram: false
sdmmc: false
block_device_interface: false
width: 960
height: 540
fb_bytes: 64800
controllers: [LgfxEpd]
grayscale: 16-gray
viewable_insets: {top: 9, right: 3, bottom: 3, left: 3}
touch: GT911
multitouch: true
has_home_key: false
frontlight: none
ui_scale: 1.2
ppi_note: ~234 PPI comment in BoardConfig; uiScale is hand-tuned
usb_detect: gpio5
shared_pads: {}
caps: [TOUCH, RTC, BUZZER]
---

# M5Stack PaperS3

ESP32-S3. Same 960×540 16-gray ED047TC1 glass as M5Paper v1.1, but no
IT8951 — parallel bus via `LgfxEpdDriver` (same class as LilyGo). No GPIO
buttons; navigation is touch-only. GT911, BM8563 RTC, LEDC buzzer, SPI SD,
GPIO3 ADC battery, GPIO5 USB detect. No power latch; off is
`BoardPaperS3::powerOff()` (GPIO44 pulse). Framebuffer stays in DRAM
(`FREEINK_FB_PSRAM` is not default-on for this device). `uiScale` 1.2.

Own sample env (`papers3`): `-DFREEINK_DEVICE_PAPERS3=1` and
`-DBOARD_HAS_PSRAM` (M5GFX OPI PSRAM). See `docs/m5stack-papers3-support.md`.
