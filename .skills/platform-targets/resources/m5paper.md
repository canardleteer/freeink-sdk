---
device: m5paper
device_flag: FREEINK_DEVICE_M5PAPER
sdk_profile: M5PAPER_V11
sdk_header: libs/hardware/BoardConfig/include/BoardConfig.h
shared_binary_envs: [m5paper_v11]
mcu_family: esp32
board_package: esp32dev
psram_in_ini: true
psram_on_silicon: true
fb_in_psram: true
sdmmc: false
block_device_interface: false
width: 960
height: 540
fb_bytes: 64800
controllers: [IT8951]
grayscale: 16-gray
viewable_insets: {top: 9, right: 3, bottom: 3, left: 3}
touch: GT911
multitouch: true
has_home_key: false
frontlight: none
ui_scale: 1.2
ppi_note: ~234 PPI comment in BoardConfig; uiScale is hand-tuned
usb_detect: none
shared_pads:
  13: sd-miso
caps: [TOUCH]
---

# M5Paper v1.1

Classic ESP32 (ESP32-D0WDQ6), not C3 or S3 — never shares a binary with
those families. 960×540 landscape framebuffer rotated onto a 540×960
ED047TC1 behind an IT8951E. GT911 touch, rotary wheel, GPIO35 ADC battery.
`FREEINK_FB_PSRAM` defaults on (63 KB framebuffer will not fit in classic
ESP32 DRAM). Power latch GPIO2. `uiScale` 1.2.

Own sample env (`m5paper_v11`): `-DFREEINK_DEVICE_M5PAPER=1` and
`-DBOARD_HAS_PSRAM`.
