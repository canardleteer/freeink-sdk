# FreeInk SDK

A hardware-independent e-paper reader SDK: board profiles, panel drivers, and
device managers behind a stable `EInkDisplay` / `InputManager` /
`BatteryMonitor` / `SDCardManager` / `BoardConfig` API. This repository is a
**set of libraries**, not a firmware app. There is no root `platformio.ini`.

Which devices this tree **ships** is every `FREEINK_DEVICE_*` in
`BoardConfig.h`. Per-device numbers live in the `platform-targets` skill.
Human overview and architecture live in `README.md`. Prefer that file and
`docs/` over restating them here.

## Agent Documentation Standards

Project-local skills exist under `.skills/` and should remain discoverable
by agents working in this repository. Maintain those skills according to the
[Agent Skills specification](https://agentskills.io/specification), and
maintain this file according to the
[AGENTS.md standard](https://agents.md/). Keep both portable across
compatible agent clients, without assumptions about user-specific paths or
session state.

This file is clone-local and untracked. If you add a worktree, copy
`AGENTS.md` from the worktree you started in into the new worktree root. If
`.skills/` is still untracked there, copy it too.

## Working rules

- Cite a file and a function, type, or constant name. Do not cite line
  numbers. The file — and the signature if needed — is what disambiguates.
- Hardware facts (MCU, PSRAM, panel size, controller, framebuffer bytes,
  touch, multitouch, home key, frontlight, USB detect, shared GPIO pads,
  `uiScale`, bezel insets) **differ by device**. Read them from the
  `platform-targets` skill — not from this file.
- Prefer in-tree `BoardConfig.h` for hardware and SDK APIs on this
  checkout. `https://freeink.org/llms.txt` is a website index, not that
  header. Neither a consumer firmware tree nor that index overrides the
  in-tree header for facts here.
- Never hardcode panel size. Use the active profile's
  `displayWidth` / `displayHeight` and `BoardConfig::MAX_FRAMEBUFFER_BYTES`
  for the compiled set.
- PSRAM / `MALLOC_CAP_SPIRAM` only when the **env being built** sets
  `BOARD_HAS_PSRAM`. S3 silicon does not imply that.
- One env may set several device flags (shared binary). Treat each flag as
  its own device until that env is split. Open a `resources/` file when the
  change compiles that flag or the task names that device. When
  `BoardConfig.h`, `platformio.sample.ini`, or that skill's schema moves,
  refresh every resource **and** the schema's per-field option comments.
- A vendor product page is not a released unit. Cite it from the matching
  `platform-targets` resource (schematic PDFs that the page serves are the
  same vendor source, not a second origin). Resource YAML follows current
  firmware on this checkout. Until a released unit is measured, list
  divergences — do not rewrite drivers or profiles to match the page.

## Worktrees

Do not edit, build, or commit in a checkout another person or agent is
using. Never run two `pio run` processes in the same project directory.

Not required when this checkout is already yours alone. If two agents or
people would otherwise share a tree, a dedicated git worktree is a
consistent way to isolate them. One concurrent agent, one worktree.

1. Branch from `main` unless the task continues an existing branch.
2. Add a sibling worktree (name from the task slug; follow this clone's
   branch naming):

   ```sh
   git worktree add -b <branch> ../freeink-sdk-<slug> main
   ```

3. Copy this `AGENTS.md` (and `.skills/` if it is untracked) into the new
   root.
4. In that worktree: `git submodule update --init` (Lucide icons at
   `libs/assets/Icons/lucide`).
5. Work, build, and test only inside that directory. Remove the worktree
   when the task is finished (`git worktree remove`).

## PlatformIO: shared tools, isolated builds

Firmware that consumes this SDK is built with PlatformIO / pioarduino
(Arduino-ESP32 3.3.x, ESP-IDF 5.5.x). Toolchains and frameworks are large;
builds and `.pio` trees must not be. Split them:

| Share across worktrees (tools / downloads) | Isolate per worktree (outputs) |
|---|---|
| `PLATFORMIO_CORE_DIR` (default `~/.platformio`) | `PLATFORMIO_WORKSPACE_DIR` (default `<project>/.pio`) |
| `packages/` (compiler, esptool, Arduino-ESP32, ESP-IDF) | `PLATFORMIO_BUILD_DIR` (`<project>/.pio/build`) |
| `platforms/` | `PLATFORMIO_LIBDEPS_DIR` (`<project>/.pio/libdeps`) |
| `.cache/` (downloaded platform zips) | Firmware `.bin` / `.elf` / compile_commands |

Leave `PLATFORMIO_CORE_DIR` at the shared default. Do **not** set
`core_dir` to a path inside a worktree (that duplicates multi-hundred-MB
toolchains). Do **not** point `PLATFORMIO_WORKSPACE_DIR`,
`PLATFORMIO_BUILD_DIR`, or `PLATFORMIO_LIBDEPS_DIR` at the core dir or at
any other shared folder.

A worktree already has its own project directory, so the default
`<project>/.pio` is isolated. That is the whole isolation mechanism — keep
it.

Optional extra reuse (still not a substitute for a per-worktree `.pio`):

```sh
# Content-addressed object cache. Safe to share; misses are fine.
export PLATFORMIO_BUILD_CACHE_DIR="${XDG_CACHE_HOME:-$HOME/.cache}/platformio-build-cache"
```

If `ccache` is on the PATH, a shared `CCACHE_DIR` is likewise safe.

Never run two `pio run` processes in the **same** project directory at
once. Two worktrees sharing `~/.platformio` is expected; two processes
sharing one `.pio/build/<env>` will clobber firmware and object files.

Optional cache warmth (sequential `pio run` in a **firmware** worktree) is
in the `platform-targets` skill. Skip it unless you want a warm `.pio/`.

### Consumer firmware checkouts

This SDK is pulled into a firmware tree with `Name=symlink://…` `lib_deps`
(`platformio.sample.ini`, `platformio.crosspoint.sample.ini`). Object files
land in the **firmware** project's `.pio`, not here.

- If two agents would share a firmware tree, give each SDK checkout its
  own firmware worktree (or a unique `PLATFORMIO_WORKSPACE_DIR` under
  that firmware tree).
- Point every `symlink://` path at **this** SDK checkout, not a shared
  tree another agent is editing.
- Do not let two agents `pio run` in one firmware working tree, even if
  their SDK checkouts differ.

Copy `platformio.sample.ini` into a firmware project; do not add a root
`platformio.ini` to this SDK. For CrossPoint, copy
`platformio.crosspoint.sample.ini` as that repo's `platformio.local.ini`
and override only `[base].lib_deps` — do not redefine env `build_flags` or
device selection disappears.

Every firmware env must pass at least one `-DFREEINK_DEVICE_*`. Mixing
ESP32-C3, ESP32-S3, and classic ESP32 in one binary is a compile error.
Sample env names, `BOARD_HAS_PSRAM`, and `USE_BLOCK_DEVICE_INTERFACE` live
in the `platform-targets` skill.

## Host tests (no device, no PlatformIO)

Prefer these for library logic. Scripts write under `$TMPDIR` with **fixed
subdirectory names**, so a shared `/tmp` **will** clobber parallel agents.
Before any host test:

```sh
export TMPDIR="${PWD}/.cache/tmp"
mkdir -p "$TMPDIR"
```

`.cache/` is gitignored. Then:

```sh
sh libs/ui/FreeInkUI/test/host/run.sh
sh libs/book/FreeInkBook/test/host/run.sh
sh libs/hardware/InputManager/test/host/run.sh
```

| Change | Run |
|---|---|
| FreeInkUI layout, routing, components | FreeInkUI host suite; new UI logic gets a host test beside it |
| FreeInkBook parse/layout/cache/fonts | FreeInkBook host suite |
| GT911 multi-touch gesture math | InputManager host suite |
| Display drivers, BoardConfig, device managers | A consumer `pio run` for the affected env when a firmware tree is available; do not claim a panel was verified without that build |

Host libraries are freestanding C++17 (`-Wall -Wextra -Werror`). Firmware
code uses `-std=gnu++2a` as in `platformio.sample.ini`.

UI gallery / builder (optional, not a substitute for tests):

```sh
sh libs/ui/FreeInkUI/tools/render_gallery.sh
python3 libs/ui/FreeInkUI/tools/builder/server.py
```

## Architecture rules

Keep generic code hardware-independent. Device *names* and
`FREEINK_DEVICE_*` flags live only in `BoardConfig`. Facade, drivers,
input, and SD key off derived `FREEINK_DRIVER_*` / `FREEINK_CAP_*` and
injected config or hooks — never a device name.

- New device: a `BoardProfile`, a `FREEINK_DEVICE_*` flag, a sample env,
  a `platform-targets` resource, and a driver **config** (LUTs/waveforms).
  Reuse an existing `PanelDriver` when the controller matches. Resolution
  is always profile `displayWidth` / `displayHeight`.
- Board quirks that are not plain config (for example an SD rail behind an
  I²C PMIC) go through hooks, not `if (device == …)` in managers.
- Public includes stay the compat names (`<EInkDisplay.h>`, type
  `EInkDisplay`) unless the task is deliberately using `freeink::`
  internals.
- `#include` and link heavy third-party display stacks only inside the
  driver's `FREEINK_DRIVER_*` guard, and only on that device's env
  `lib_deps`.

Topic docs (read the one that matches the edit):

- `docs/freeink-ui.md`, `docs/freeink-book.md`
- `docs/display-driver-references.md`, `docs/deferred-refresh-migration.md`
- `docs/consumer-mcu-portability.md`, `docs/ble-keyboard-host.md`
- Per-board: `docs/lilygo-t5s3-support.md`, `docs/m5stack-papers3-support.md`,
  `docs/xteink-x4pro-support.md`, `docs/xteink-x3-uc8279-support.md`

## Code style

Match the file you are editing. Do not reformat unrelated code. Prefer
existing `namespace freeink` patterns, capability gates, and
zero-heap/fixed-capacity designs in UI and book code. Do not add
Arduino/ESP-IDF includes to freestanding host-tested libraries.

## Git

Do not commit this `AGENTS.md` file. Follow the surrounding history for
commit messages (short imperative subject; `feat:` / `fix:` appear in
places, plain “Add …” / “Fix …” in others). Do not commit `.pio/`,
`.cache/`, or host-test binaries. Do not push or open a PR unless asked.
