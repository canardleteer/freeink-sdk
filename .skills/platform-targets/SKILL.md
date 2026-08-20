---
name: platform-targets
description: "Device and sample-env facts for the FreeInk SDK. Use when the task mentions a board, env, FREEINK_DEVICE_*, PSRAM, panel, framebuffer, controller, touch, multitouch, home key, frontlight, USB detect, shared GPIO pads, uiScale, bezel insets, or other device hardware capabilities; when adding, correcting, or removing a device or sample env; when changing this skill's resource schema; when optionally pre-warming a consumer PlatformIO .pio cache; or when AGENTS.md defers a hardware number here."
---

# Platform Targets

`AGENTS.md` states rules that hold for every device this SDK **ships**.
This skill holds per-device numbers. One resource file per **device**, not per
PIO env name, and not a closed board list.

Repo language: **env** is a PlatformIO binary (a `[env:…]` in
`platformio.sample.ini`, or a consumer firmware env). **device** is a
`FREEINK_DEVICE_*` flag. **board profile** is a `BoardProfile` in this tree.
Do not treat those three as the same thing.

If the next step is a judgment call **about something you are already
touching** (delete that resource, invent a sample env, change the schema),
**ask**. Do not guess. Do not ask about a resource or env you are not editing.

## When to open a resource

Open `resources/<device>.md` when the task names that device, env, or flag, or
when the change compiles that flag. Do not glob `resources/` and treat every
file as a must-build constraint. Files you have not opened stay out of the
union.

Keep **every** file in `resources/` current when you refresh from sources or
change the schema — including files whose flags are not on a given consumer's
compile set.

## Compile set

`BoardConfig.h` device flags ∩ `platformio.sample.ini`. This is what the SDK
**documents as buildable**. Enumerate every flag in the header; omitting one
is a lie about what this tree ships. It does **not** limit which resource
files may exist or be refreshed.

This repository has no root `platformio.ini` and no firmware CI matrix. A
consumer's must-build union is that consumer's committed INI ∩ CI — not this
tree.

1. **Ships.** Every `FREEINK_DEVICE_*` in
   `libs/hardware/BoardConfig/include/BoardConfig.h`. That is the SDK device
   set. `XTEINK_X3_UC8279` is a sibling profile of `FREEINK_DEVICE_X3`, not
   its own flag — keep it on `x3.md`.
2. **Sample envs.** Parse `platformio.sample.ini` for `[env:…]` (skip
   commented examples) and that env's `-DFREEINK_DEVICE_*`,
   `-DBOARD_HAS_PSRAM`, `-DUSE_BLOCK_DEVICE_INTERFACE`. If the header has a
   flag with no uncommented sample env, say so. Do not invent a sample env.
3. **Must-build for a change in this SDK.** For each flag the change
   compiles, read the matching resource. One sample env may set several
   flags (`[env:xteink]` is X3+X4); load every matching file. Other resource
   files do not widen it.
4. **Conflict.** If a number here disagrees with `AGENTS.md`, this skill and
   `BoardConfig.h` win. Fix the guide. Hardware numbers come from the
   header, not from a consumer firmware tree.

`platformio.local.ini` in a consumer is desk-only. A local-only env is a
question only if the task is about that env. Respect a no.

Hardware truth for this checkout is
`libs/hardware/BoardConfig/include/BoardConfig.h`.

`https://freeink.org/llms.txt` is a website index — useful for general
hints, not that header. Do not fill resource fields from it. It does not
override the in-tree header.

## Warm the PlatformIO cache

Not required. This SDK does not have a project `.pio/`. Skip this unless
you want a warm **consumer** `.pio/`. Compiling only the env the task needs
is enough.

Warm in the **firmware** worktree that `symlink://`s this SDK checkout —
never in this tree, and never two `pio run -e` at once in the same firmware
directory (they share that project's `.pio/`). Share
`PLATFORMIO_CORE_DIR` (default `~/.platformio/`) across worktrees. Leave
each firmware worktree's `.pio/` isolated. Firmware compile only; do not
flash or attach hardware.

A representative sample-env order when the consumer follows
`platformio.sample.ini` (not every env; update from the sample file if it
grows or shrinks):

```bash
pio run -e xteink
pio run -e x4pro
pio run -e m5paper_v11
pio run -e papermono
```

`xteink` first: C3, X3+X4 in one binary, tightest DRAM. `x4pro` pulls the
S3 toolchain and the three X4-family drivers (SSD1677 / UC8179 / UC8279).
`m5paper_v11` is the classic-ESP32 family. `papermono` fills SDMMC +
`FREEINK_FB_PSRAM`. If the consumer has its own compile set, warm from
**that** INI ∩ CI instead.

`pio run -t clean` throws that warmth away. Incremental rebuilds after
checking out a close branch stay fast if you leave `.pio/` in place.

## Interactions

Match the task. Do not assume one of these is the only way in.

1. **Add a sample env / device flag.** Create or refresh that device's
   resource from the field map. This skill owns the resource; it does not
   invent `platformio.sample.ini` edits unless the task includes those.
2. **Correct facts.** A resource disagrees with `BoardConfig.h` or the
   sample INI. Re-read the sources, patch the file, say what changed. Do
   not "fix" drivers to match stale prose.
3. **Remove an env or a resource.** **Ask** whether to delete the resource
   only if that file or env is in the change. Do not propose deleting a
   sibling file you are not touching.
4. **Change the schema.** Edit [SCHEMA.md](SCHEMA.md) — fields **and** the
   per-item comments that list options — then every file in `resources/` so
   none omit the new field or still name a deleted option. Do not drive-by
   schema-edit.
5. **Experiment / local build cleanup.** Follow the compile set on this
   checkout. Ask about deleting a resource only if that file is part of the
   experiment.

## Refreshing resources from sources

Field meanings live in [SCHEMA.md](SCHEMA.md). This skill may edit its own
`resources/` and `SCHEMA.md`.

If **you** change a source this skill reads, update the dependents in the
same change. Do not leave SCHEMA comments or resource files describing the
old integration. Sources: `BoardConfig.h` (profiles, enums, `FREEINK_CAP_*`,
`FREEINK_FB_PSRAM`, `ViewableInsets`, `TouchConfig.hasHomeKey`),
`InputManager::supportsMultiTouch` (for `multitouch`),
`BoardProfile.usbDetect` (for `usb_detect`), `platformio.sample.ini`, and
[SCHEMA.md](SCHEMA.md) itself.

When any of those move, refresh **all** resource files, not only the ones
on a consumer compile set, and re-vet SCHEMA comments against the
integrations and against every file in `resources/` (see SCHEMA.md).

| Ask | Source |
| --- | --- |
| Which `[env:…]` exist here? | uncommented envs in `platformio.sample.ini` |
| Which device flags does each env set? | that env's `-DFREEINK_DEVICE_*` |
| Which devices does the SDK ship? | every `FREEINK_DEVICE_*` in `BoardConfig.h` |
| Shared-binary aliases? | same flags on other sample envs of that family |

| Field | Source |
| --- | --- |
| `device` / `device_flag` | `FREEINK_DEVICE_*` (sample INI if present, else BoardConfig / the requested flag) |
| `sdk_profile` / `sdk_header` | `constexpr BoardProfile` in `BoardConfig.h` |
| `shared_binary_envs` | every uncommented sample env on **this** tree that sets this flag (`[]` if none) |
| `board_package` | `board =` on those envs, or sample comments / profile comments if none |
| `mcu_family` | that PlatformIO board / `board_build.mcu` / `FREEINK_MCU_*` |
| `psram_in_ini` | `-DBOARD_HAS_PSRAM` on those sample envs (false if no such env here) |
| `psram_on_silicon` | `BoardProfile` comments / chip |
| `fb_in_psram` | `FREEINK_FB_PSRAM` default for this device in `BoardConfig.h` |
| `sdmmc` | `BoardProfile.sdmmc.busWidth != 0` |
| `block_device_interface` | `-DUSE_BLOCK_DEVICE_INTERFACE` on those sample envs (false if none) |
| `width` / `height` | `BoardProfile.displayWidth` / `displayHeight` |
| `fb_bytes` | `width/8 * height` for **this** profile. Compiled max is `BoardConfig::MAX_FRAMEBUFFER_BYTES` for the flags on that env. |
| `controllers` | `DisplayController` plus sibling profiles selected at runtime for that device |
| `grayscale` | driver / profile comments in `BoardConfig.h` |
| `viewable_insets` | `BoardProfile.viewableInsets` if set; else the `ViewableInsets` member defaults (9/3/3/3, not zeros) |
| `touch` / `frontlight` / `ui_scale` | `touch`, `frontlight`, `uiScale` on the profile |
| `multitouch` | `InputManager::supportsMultiTouch` — true only when `BoardProfile.touch.controller` is `TouchController::Gt911`. Other controllers stay single-contact. |
| `has_home_key` | `BoardConfig::hasHomeKey` / `TouchConfig.hasHomeKey` |
| `usb_detect` | `gpioN` from `BoardProfile.usbDetect` if `>= 0`, else `none`. Record the profile pin. A consumer may remap (X3 often reads BQ27220 instead of pin 20). Do not copy a raw `usbDetect >= 0` as a VBUS pin without reading how that consumer uses it. |
| `shared_pads` | This device's role for pads that collide across devices that can share a binary (today GPIO13 / GPIO20 on X3+X4) or whose SDK use differs from the raw profile field. Not a full pinout. |
| `ppi_note` | SDK comments only; do not invent dpi |
| `caps` | `FREEINK_CAP_*` macros in `BoardConfig.h` that include this device |

Do not take panel size, PSRAM, or caps from `AGENTS.md`, `llms.txt`, or a
consumer firmware checkout. Those may inform a new-support review; they do
not override in-tree `BoardConfig.h`.

A vendor product page is not a released unit. Cite that page in the
resource body. If a schematic PDF is a sublink of the page, cite it
under the page — that is one vendor source, generally authoritative for
the published pin map / sheet. A GitHub attachment of the same file is a
rehost, not a second origin. YAML follows the table above (current
checkout firmware). Until a released unit is measured, list divergences
and the first source of each firmware choice — do not rewrite firmware
to match the page.

## Self-review

- [ ] Opened only the resources the task or the compile-set flags called for.
- [ ] Compile-set enumeration listed every header flag and said which ones
      lack an uncommented sample env.
- [ ] Must-build union used only the flags the change compiles, not every
      file in `resources/`.
- [ ] If a relied-on source moved, refreshed every resource **and** SCHEMA
      comments; no leftover option or missing new option.
- [ ] Did not ask about a file or env I was not touching.
- [ ] Asked before deleting a resource that *is* in the change; respected a no.
- [ ] PSRAM / `MALLOC_CAP_SPIRAM` only if the env being built sets
      `BOARD_HAS_PSRAM`; the tightest compiled DRAM target still builds
      without that allocation.
- [ ] Did not run two `pio run -e` at once in one firmware tree. Did not
      `pio run -t clean` a warm `.pio` unless asked.
- [ ] Did not rewrite firmware to match a vendor page. YAML is current
      checkout firmware; divergences stay in the resource body until a
      released unit is measured.
