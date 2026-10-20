# Open Sonic — PortMaster

A PortMaster package for Open Sonic, a free 2D platformer inspired by the classic Sonic the Hedgehog games.

## Project Information

| Property | Value |
|---|---|
| Game | Open Sonic |
| Upstream website | https://opensnc.sourceforge.net/ |
| Developer listed in `gameinfo.xml` | Alexandre Martins |
| Game genre | Platformer |
| Release date listed in `gameinfo.xml` | September 19, 2010 |
| Package metadata version | `4` |
| Package name in `port.json` | `opensonic.zip` |
| Target architecture | AArch64 |
| Minimum glibc declared in `port.json` | `2.17` |
| PortMaster launcher | `Open Sonic.sh` |
| Game executable | `opensonic/opensonic.aarch64` |
| Controller configuration | `opensonic/opensonic.ini` |

The package is intended to run Open Sonic through PortMaster on a compatible ARM64 Linux environment.

## Game Description

Open Sonic is a free, open-source 2D platformer inspired by the classic Sonic the Hedgehog games.

The description included in `port.json` identifies these features:

- Fast, loop-filled levels.
- Shields.
- Bosses.
- A built-in level editor.

The game frontend metadata in `gameinfo.xml` additionally describes collectible rings and bundled quests.

Official website: https://opensnc.sourceforge.net/

## Package Contents

The following paths are present in the supplied ZIP.

### Root files

| File | Purpose |
|---|---|
| `README.md` | Package documentation, controls, and compilation instructions |
| `port.json` | PortMaster package metadata |
| `Open Sonic.sh` | PortMaster launch script |
| `gameinfo.xml` | Game frontend metadata |

### Game files

| Directory or file | Contents |
|---|---|
| `opensonic/opensonic.aarch64` | Prebuilt ARM64 game executable |
| `opensonic/opensonic.ini` | Controller mapping configuration |
| `opensonic/libs.aarch64/` | Bundled AArch64 shared libraries |
| `opensonic/libs.aarch64/allegro/` | Allegro ALSA digital audio plugin |
| `opensonic/config/` | `samples.def` |
| `opensonic/images/` | PNG graphics and image assets |
| `opensonic/levels/` | Level files, including tutorial and other levels |
| `opensonic/languages/` | Language files, including `english.lng` and `indonesian.lng` |
| `opensonic/musics/` | OGG music files and `credits.txt` |
| `opensonic/objects/` | Object definition files |
| `opensonic/quests/` | Quest files |
| `opensonic/samples/` | WAV and OGG sound effects |
| `opensonic/sprites/` | Sprite definition files |
| `opensonic/themes/` | Background, theme, and interface resource files |
| `opensonic/licenses/` | License texts for Open Sonic and bundled libraries |

The archive contains a prebuilt executable and game resources. It does not contain the complete upstream source tree required by the compilation instructions.

## Executable Verification

The file `opensonic/opensonic.aarch64` was inspected using `file` and `readelf`.

| ELF property | Observed result |
|---|---|
| Format | ELF |
| Class | ELF64 |
| Byte order | Little-endian |
| Machine | AArch64 |
| ELF type | `ET_DYN` — position-independent executable |
| Linking | Dynamically linked |
| Interpreter | `/lib/ld-linux-aarch64.so.1` |
| Build ID | `e50b22db28b01d7908506c2ce7e8b450b51bc93c` |

The executable declares these direct shared-library dependencies:

```text
libm.so.6
liblogg.so.4.4
libjpgalleg.so.4.4
libloadpng.so.4.4
liballeg.so.4.4
libc.so.6
```

The package includes files corresponding to the Allegro, JPGalleg, loadpng, and LOGG libraries under `opensonic/libs.aarch64/`.

The presence of the bundled libraries does not establish that every runtime dependency is satisfied. Dependencies must be checked against the actual target system.

## PortMaster Metadata

The included `port.json` declares:

| Field | Value |
|---|---|
| `version` | `4` |
| `name` | `opensonic.zip` |
| `items` | `Open Sonic.sh`, `opensonic` |
| `genres` | `platformer` |
| `rtr` | `true` |
| `exp` | `false` |
| `availability` | `full` |
| `arch` | `aarch64` |
| `min_glibc` | `2.17` |
| `runtime` | Empty array |
| `reqs` | Empty array |

These are the values declared in the supplied metadata. They are not independent proof of compatibility with every PortMaster device.

## Launch Script

The included `Open Sonic.sh` performs the following operations.

1. Determines the PortMaster control directory from the available locations.
2. Sources `control.txt` and calls `get_controls`.
3. Sets the game directory to `/$directory/ports/opensonic`.
4. Selects the executable as `opensonic.${DEVICE_ARCH}`.
5. Selects `opensonic.ini` as the controller configuration.
6. Adds `libs.${DEVICE_ARCH}` to `WRAPPED_LIBRARY_PATH`.
7. Redirects launch output to `opensonic/log.txt`.
8. Creates the `conf/` directory.
9. Checks for the `weston_pkg_0.2.squashfs` runtime and invokes `harbourmaster runtime_check` if the runtime file is missing.
10. Mounts the Weston runtime under `/tmp/weston`.
11. Starts `gptokeyb2` with the `opensonic.ini` configuration.
12. Passes the original `XDG_RUNTIME_DIR` to the game when that variable is set.
13. Launches the executable through `westonwrap.sh` using `drm gl kiosk system` and `--resolution MAX`.
14. Sets the following environment variables for the game:

```text
OPENSONIC_HOME
OPENSONIC_NO_JOYSTICK
OPENSONIC_ALSA_MODULE
```

15. Calls the Weston cleanup command, conditionally unmounts the runtime, and invokes `pm_finish`.

The launcher specifically configures the game to use `gptokeyb2` for gamepad handling and disables Allegro's own joystick handling through `OPENSONIC_NO_JOYSTICK=1`.

These steps describe the script's implementation; they do not establish that every step succeeds on a physical device.

## Controller Configuration

The included controller configuration is:

`opensonic/opensonic.ini`

The README and configuration file document the following mappings.

| Controller input | Action or keyboard mapping |
|---|---|
| D-pad | Directional movement |
| Left stick | Directional movement |
| A | Jump / Confirm |
| B | Secondary action |
| Start | Pause / Confirm |
| Select | Back / Quit |
| Select + Start | Exit through `gptokeyb2` |

The INI maps controller inputs to keyboard keys used by the game:

| INI control | Key |
|---|---|
| `a` | `space` |
| `b` | `leftctrl` |
| `start` | `enter` |
| `back` | `esc` |
| `up` | `up` |
| `down` | `down` |
| `left` | `left` |
| `right` | `right` |
| `left_analog` | `arrow_keys` |

The `x`, `y`, shoulder buttons, and right analog mapping are empty in the supplied configuration.

The configuration also declares:

```ini
[config]
deadzone_triggers = 3000
deadzone_mode = axial
deadzone = 2000
deadzone_scale = 8
```

The Select + Start exit shortcut is handled by `gptokeyb2`, according to the comments in the supplied files.

## Build Instructions

The included `README.md` documents a source-build procedure for an AArch64 Debian 12 or Ubuntu 22.04-or-newer environment, or a corresponding chroot, run as root.

### Dependencies listed in the supplied README

```bash
apt-get update
apt-get install -y \
  build-essential \
  cmake \
  patch \
  pkg-config \
  wget \
  liballegro4-dev \
  libloadpng4-dev \
  libjpgalleg4-dev \
  liblogg4-dev \
  libpng-dev \
  zlib1g-dev \
  libogg-dev \
  libvorbis-dev
```

### Additional build inputs

The supplied instructions refer to these external inputs:

- `opensnc-src-0.1.4.tar.gz`, obtained from the Open Sonic project website.
- `loggcompat-4.4.2.tar.gz`, obtained from the URL specified in the supplied README.
- `patches/0001-portmaster-env-hooks.patch`, referenced by the build instructions.

The source archive, LOGG compatibility archive, and `patches/` directory are not present in the supplied ZIP. Therefore, the included build instructions are not self-contained within the archive.

### Build commands documented in the supplied README

After obtaining the required external inputs, the README specifies:

```bash
tar -xf opensnc-src-0.1.4.tar.gz
cd opensnc-src-0.1.4

patch -p1 < ../patches/0001-portmaster-env-hooks.patch
cp ../loggcompat-4.4.2/logg-4.4.2.h ./logg.h

export _ALLEGRO_LIBS="$(allegro-config --libs) -ldl"
export _ALLEGRO_VERSION="$(allegro-config --version)"

cmake . -DCMAKE_BUILD_TYPE=Release \
  -DCMAKE_C_FLAGS="-fcommon -Wno-error -Wno-implicit-function-declaration -Wno-int-conversion -Wno-incompatible-pointer-types -I."

make -j$(nproc)
```

These commands reproduce the procedure documented in the supplied README; they are not a claim that a new build was executed successfully.

## Known Packaging Detail

The included `gameinfo.xml` references the following image:

```xml
<image>./opensonic/screenshot.png</image>
```

However, `opensonic/screenshot.png` is absent from the supplied ZIP.

This is a discrepancy between the frontend metadata and the actual archive contents. No replacement screenshot is included or assumed here.

## Testing Status

### Confirmed by archive inspection

- The ZIP contains the launcher `Open Sonic.sh`.
- The ZIP contains `port.json` and `gameinfo.xml`.
- The game executable is an ELF64 AArch64 position-independent executable.
- The executable's interpreter and direct shared-library dependencies were inspected.
- The archive contains the listed game resources and bundled AArch64 libraries.
- The archive contains `opensonic/opensonic.ini`.
- The referenced `opensonic/screenshot.png` is missing.

### Not confirmed by archive inspection

- Successful launch on physical hardware.
- Compatibility with any specific handheld model or firmware.
- Actual graphics and audio output.
- Functional controller input on a target device.
- Save persistence across launches.
- Runtime stability and performance.

These items require appropriate runtime tests and supporting logs.

## License

The ZIP includes license files under `opensonic/licenses/`, including:

- `LICENSE-opensonic.txt`
- `LICENSE-Allegro.txt`
- `LICENSE-jpgalleg.txt`
- `LICENSE-loadpng.txt`
- `LICENSE-logg.txt`
- `LICENSE-libogg.txt`
- `LICENSE-libvorbis.txt`
- `LICENSE-libvorbisfile.txt`

Review the relevant license files before redistributing or modifying the package. The archive contains separate license texts for the game and bundled components.

## Upstream Reference

- Official website: https://opensnc.sourceforge.net/
- Official downloads: https://opensnc.sourceforge.net/home/download.php
- Official FAQ: https://opensnc.sourceforge.net/home/faq.php

---

**Status:** The supplied archive contains a prebuilt AArch64 executable and PortMaster integration files. Successful operation on physical hardware has not been established by this inspection.
