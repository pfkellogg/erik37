# Erik37 + FM-1 Big Screen (unofficial, work in progress)

> **Unofficial, not reviewed by Erik37's author.** This branch is a proposal: I've asked genkigenki whether they'd
> like it in Erik37. Until they answer there's no download here, on purpose. Please don't build and pass it around.

[Erik37](https://github.com/genkigenki/erik37), genkigenki's grand piano for the M-Vave SMK-37 and the M-VAVE FM-1,
with the FM-1 Big Screen mirror added: the FM-1's screen shows big and live in
[FM-1 Mirror](https://github.com/pfkellogg/fm1-mirror) (Mac, Windows, Linux) and
[FM-1 Mirror + Spyglass](https://github.com/pfkellogg/fm1-mirror-spyglass-android) (Android), **over USB**, with the
voice playing ("ERIK37 CONCERT") on top of the spyglass. Everything else is Erik37 1.3 as genkigenki made it.

Tested on an FM-1 (2026-11-14) with FM-1 Mirror 1.3.7 on a Mac: the play screen shows big, the strings and keys light
up as you play, and the dynamic marking follows. Not yet tested: the settings pages, FM-1 Mirror + Spyglass on
Android, and the newer FM-1 units with the other flash chip.

## What's different from Erik37 1.3

- **`app/big_screen.c`** (with `app/screen_mirror.c` / `.h`, MIT): while an app says hello over USB
  (`F0 7D 'F' 'S' 00/01/02 F7`, about once a second), each LCD rectangle also goes out as SysEx. Nobody asking:
  nothing is done (`bs_lcd` returns at once).
- **Only what changed:** a 16-bit hash of each 40-pixel segment of each row; only changed segments go out.
- **The piano never waits on the screen:** a new `plat_sysex_try` in `base/device/unity.c` puts a message in the USB
  SysEx ring only if there's room (Erik37's `plat_sysex_send` may wait up to 200 ms). A row that doesn't fit is
  dropped and the page is drawn again a moment later.
- **Hooks:** `app/app.h` (`ERIK_BIG_SCREEN`, `APP_SYSEX`), `app/app.c` (`app_sysex`, `bs_service` in `app_frame`),
  `base/device/lcd.c` (`bs_lcd` in `lcd_fill` / `lcd_blit`). The SMK-37 builds and the host simulator are unchanged
  (the hooks are FM-1 device-only).
- **Memory:** RAM `.data+.bss` 82,932 B of 98,304 (Erik37 1.3: 77,428); the pool is unchanged.
- **Build on a Mac without Docker:** `base/tools/build.py` can run JieLi's Windows toolchain under Wine
  (`JIELI_WINE`, `JIELI_WIN_BIN`).

## Not in this branch: Bluetooth

Erik37 is built without Bluetooth (`fm1.toml` `ble = false`), and its pool is nearly full (333 KB of 344 KB), so the
screen goes over USB only.

## Credits

Erik37 by genkigenki (M. Geisler), MPL-2.0. The piano samples: "Salamander Grand Piano V3" by Alexander Holm,
CC BY 3.0 (https://creativecommons.org/licenses/by/3.0/), resampled, trimmed, looped and compressed for Erik37.
fm1-base by Charles Vestal, felucca-bsp by Leo Kuroshita (Hügelton Instruments), MPL-2.0. Big Screen: Patrick
Kellogg ([FM-1 Mirror](https://github.com/pfkellogg/fm1-mirror)).
