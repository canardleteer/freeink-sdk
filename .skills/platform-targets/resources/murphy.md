---
device: murphy
device_flag: FREEINK_DEVICE_MURPHY
sdk_profile: MURPHY_M3
sdk_header: libs/hardware/BoardConfig/include/BoardConfig.h
shared_binary_envs: [murphy]
mcu_family: s3
board_package: esp32-s3-devkitc1-n16r8
psram_in_ini: false
psram_on_silicon: true
fb_in_psram: false
sdmmc: false
block_device_interface: false
width: 416
height: 240
fb_bytes: 12480
controllers: [UC8253]
grayscale: B/W
viewable_insets: {top: 9, right: 3, bottom: 3, left: 3}
touch: CHSC6x
multitouch: false
has_home_key: false
frontlight: pwm
ui_scale: 1.0
ppi_note: null
usb_detect: none
shared_pads:
  13: i2c-sda
caps: [TOUCH, FRONTLIGHT, AUDIO, BUZZER]
---

# Murphy M3

ESP32-S3. CrowPanel 3.7″ UC8253. Framebuffer is landscape 416×240; the
panel is a 240×416 controller held rotated 90°, and the Murphy driver
rotates each plane into controller RAM. CHSC6x touch (IRQ-driven), PWM
frontlight, ES8388-compatible codec + LEDC buzzer. GPIO13 is touch/codec
I2C SDA (the SPI SD pin guess in the profile conflicts with proven I2S;
audio is the verified owner).

Own sample env (`murphy`). Distinct from Murphy M4.
