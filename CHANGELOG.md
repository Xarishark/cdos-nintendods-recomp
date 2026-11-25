# Changelog

## Unreleased

### Fixed & Improved
- **Seamless True 2D Widescreen `16:9` (`342×192`, ADR-017):**
  - Fixed `1×N` (`256 px`) hardware 2D `BG2` parallax sky/castle backdrops cutting off to black at `x = 256` (e.g., Lost Village Bridge `0x022DD9F8` and Plaza `0x022DFCF8`): `title_patches.cpp` detects `1×N` HW `BG2` layers (`flags & 0x80`) and instructs `gpu2d.cpp` (`decode_text_line_n`) to wrap screenblock 0 modulo 256 px cleanly across `x = 0..341`.
  - Fixed the `x = 271` vertical mist/atmosphere brightness seam by expanding untextured axis-aligned fullscreen 3D atmospheric quads (`0..255..276 px`) to full `RenderWidth` (`342 px`) in `native_3d_raster.cpp` (and melonDS `GPU3D.cpp` fallback) without mutating shared vertices.
- **Curated Linear-Light 2D Upscale Filters & Clean `F1` Graphics Menu (ADR-017):**
  - Replaced noisy default `xBR 2x` + `Sharpen: Subtle` with exact linear sRGB analytical **`Pixellate`** (`filter_mode = 2u`, new default) + **`Pixel-AA`** (`filter_mode = 6u`, 1.5 px cubic smoothstep) + upgraded **`Bandlimited Pixel`** (`filter_mode = 5u`, TheMaister cosine-lobe integral in IEC 61966-2-1 linear sRGB), plus `Nearest` and `Bilinear`, compiled to both DXIL `ps_6_0` (`20,940 B`) and Vulkan 1.1 SPIR-V (`38,284 B`).
  - Fixed the mid-texel split bug in GPU `Sharpen` (now samples true `±1` texel neighbors, default `Off`), upgraded `Scanlines`, `CRT Aperture`, and `NDS LCD Grid` to moiré-free analytical cosine waves attenuated by `fwidth`, and decluttered `F1 -> HD Graphics` (curated 5-filter dropdown, collapsible Advanced Image Calibration, conditional CRT curvature, PiP keeping crisp pixel scalers, legacy INI keys still accepted).

### Added
- **Expanded Display & Graphics Suite (benchmarked against top GitHub recomps — `Zelda64Recomp`, `UnleashedRecomp`, `zelda3`):**
  - **Display Controls (`F1 -> Screen & Layout` + `recomplauncher.json`):** live **Monitor Selector** (multi-monitor SDL3 display detection with resolution & Hz), **Windowed Scale Presets** (`Auto`, `2x..6x` with usable-bounds clamping), **21:9 Ultrawide Cinema** aspect mode (`ultrawide-21x9`), **Display Safe Zone / Overscan Margin** (`0..10%`, preserving exact pixel grid and touch mapping), **Widescreen HUD Alignment** (`Edge-Anchored 16:9` vs `Centered 4:3 Safe Zone`), **3-Mode VSync** (`Off`, `On`, `Adaptive` with automatic fallback), and live **120 Hz Frame Interpolation** toggle (`>= 100 Hz` monitors).
  - **GPU Post-Processing Shader (`DosShaderParams` 80 B, compiled to DXIL `ps_6_0` + Vulkan 1.1 SPIR-V):** added live **Gamma** (`70..150%`), **Color Temperature** (`-30 Cool <-> +30 Warm`), **Soft Highlight Bloom** (`0..50%` 8-tap bright-pass), **Display Mask Intensity** (`0..100%`), **CRT Screen Curvature & Bezel** (`0..30%`, `fwidth`-safe non-divergent derivatives), **3D MSAA** (`Off / 2x / 4x / 8x`) persistence in `F1 -> HD Graphics`, and **4 One-Click Visual Profiles** (*Modern HD Crisp*, *Pixel Purist*, *Classic CRT*, *DS Handheld*).
- **Anti-shimmer GPU scaling shaders (Direct3D 12 DXIL `ps_6_0` + Vulkan 1.1 SPIR-V):**
  - Upgraded **Sharp Bilinear** (`sharp-bilinear`) to automatic per-axis integer prescaling with `fwidth` subpixel edge blending so stretching to any aspect ratio (`16:9`, `16:10`, `21:9`, arbitrary window sizes) stays crisp without blur or uneven pixel widths.
  - Added **Bandlimited Pixel** (`bandlimited-pixel` / `filter_mode = 5u`), a C2-continuous quintic Hermite anti-shimmer filter with gamma-2.0 linear blending to eliminate scrolling shimmer and dark halos at non-integer scales.
- **Opt-in True 2D Widescreen `16:9` (`342×192`, Experimental):**
  - Live-togglable in **`F1 -> Screen & Layout`** and `recomplauncher.json` (`dos_true_widescreen`, default `false`).
  - Expands *Castlevania: Dawn of Sorrow (USA ACVE)*'s hybrid 3D-ortho BG0 + hardware 2D BG2 (`64×32` 512px tilemap) + camera centering/clamping (`0x020F7098`) to true `342×192` (`16:9`, zero sprite stretching) in multi-screen rooms (`g_state[0x08] == 0x02` and `width_screens >= 2`), while keeping the HP/MP HUD unsplit at top-left (`center_width = 8`) and automatically letterboxing to native `256×192` on title screens, prologues, pause/equip menus, and single-screen/Save rooms.

## [1.2.0](https://github.com/Neguchads/cdos-nintendods-recomp/compare/v1.1.2...v1.2.0) (2026-11-25)


### Features

* **display-graphics:** expanded display controls and 80-byte GPU post-process shader suite ([#33](https://github.com/Neguchads/cdos-nintendods-recomp/issues/33)) ([3676bd7](https://github.com/Neguchads/cdos-nintendods-recomp/commit/3676bd7ce0fcf48d2c66803828e92300baceb9cd))
* **graphics-widescreen:** seamless True 2D Widescreen & curated linear-light upscalers ([#34](https://github.com/Neguchads/cdos-nintendods-recomp/issues/34)) ([da00e09](https://github.com/Neguchads/cdos-nintendods-recomp/commit/da00e098762c7e4030bc1ce6c032ed0900e63679))
* **video-widescreen:** Sharp/Bandlimited GPU shaders & opt-in True 2D Widescreen 342x192 ([#31](https://github.com/Neguchads/cdos-nintendods-recomp/issues/31)) ([ae19265](https://github.com/Neguchads/cdos-nintendods-recomp/commit/ae1926529b657e8036b6e4736fb2297c4fbc95dc))


### Bug Fixes

* **widescreen:** wrap 1-screen 3D parallax sky backdrops in 342x192 rooms ([#32](https://github.com/Neguchads/cdos-nintendods-recomp/issues/32)) ([1f771da](https://github.com/Neguchads/cdos-nintendods-recomp/commit/1f771da90effe4de9d5deba0822febd2e3cea173))

## 1.1.2 (2026-11-24)

### Security & Release Hardening
- **Native MSVC 2022 launcher (`CastlevaniaDawnOfSorrow.exe`):** `tools/build.py` now compiles the standalone Windows launcher with Microsoft Visual Studio 2022 (`cl.exe /std:c++20 /O2 /MT` + `rc.exe`, `443 KB` vs `1.35 MB` MinGW `-static`, importing only core Windows OS DLLs) with `LLVM-MinGW` kept as fallback, eliminating the `llvm-mingw -static` PE heuristic signature (`BitDefender Gen:Variant.Lazy.751345`).
- **Explicit `runtime/` packaging allowlist:** replaced `shutil.copytree("runtime", ...)` in `tools/package_recomplauncher.py` with an explicit 4-file allowlist (`nds_runner.exe`, `SDL3.dll`, `libc++.dll`, `libunwind.dll`) so stray files in `runtime/` can never leak into a release ZIP.
- **Toolchain & preset integrity:** pinned required SHA-256 (`5917df26...`) for `dsd.exe` in `tools/setup_toolchains.py` and switched `CMakePresets.json` Windows preset from `-march=native` to portable `-march=x86-64 -mtune=generic`.

## 1.1.1 (2026-11-24)

### Fixed
- **Portable x86-64 Windows release binaries:** switched `tools/build.py` release flags from `-march=native` to portable `-march=x86-64 -mtune=generic` so `nds_runner.exe` and `CastlevaniaDawnOfSorrow.exe` run on any x64 CPU without illegal-instruction crashes (`SIGILL`).
- **Native Windows UTF-8 paths & AV manifest hardening:** embedded [`launcher/app.manifest`](launcher/app.manifest) (`requestedExecutionLevel level="asInvoker"`, Windows 10/11 `supportedOS`, `PerMonitorV2` DPI, and `<activeCodePage>UTF-8</activeCodePage>`) into both `CastlevaniaDawnOfSorrow.exe` and `runtime/nds_runner.exe`, added UTF-8 path fallback in `runner/src/main.cpp`, and stripped unused sections (`-ffunction-sections -fdata-sections -Wl,--gc-sections -fno-ident`) from `CastlevaniaDawnOfSorrow.exe` (`2.16 MB -> 1.35 MB`) to eliminate common AV false-positive heuristics (`BitDefender Gen:Variant.Lazy`).
- **Clearer ROM boot diagnostics:** `main.cpp` now logs the exact path, byte size, and expected USA dump details (`67,108,864` bytes, `ACVE`, CRC32 `135737f6`, SHA-1 `47530ff87e608f88105a314fdf36dc385f8dec94`) when a ROM is missing, truncated, or modified.

## 1.1.0 (2026-11-24)

### Stable / Verified
- **Definitive Edition (EN & PT-BR) built in:** semantic in-memory deltas (`dos-definitive-en.dospatch`, `dos-definitive-ptbr.dospatch`) integrating *Dawn of Dignity* portraits, *Fixed Luck*, *No Required Touch Screen*, Gergoth/Death/Abaddon 100% souls fix, Black Panther behind Throne Room, and Alucard Sword as the default baseline, with **Original USA** kept as an advanced compatibility mode in `F1 -> Mods & Campaigns`.
- **Custom Music Engine (`.wav` / `.mp3` / `.ogg`, Issue #16):** native zero-DLL decoders (built-in RIFF/WAVE + `dr_mp3.h` + `stb_vorbis.c`), automatic detection of all 30 *Dawn of Sorrow* BGM tracks (`00..29`), selective Nitro SDK 2.x PXI `MUTE_TRACK` silencing (DS BGM `0` while preserving 100% of DS SFX and `STRM` voices), in-game **`F1 -> Audio` Visual Track Mapper** (assign any audio filename to any of the 30 stages), **Global Jukebox / Playlist mode** (mute all 30 DS BGMs and play selected songs continuously with Shuffle/Prev/Next), and double-clickable Windows Explorer wizard (`mods/music/Configurar_Musica.bat`).
- **2D Widescreen Display Modes (Issue #7):** **Widescreen Theatre** (`342x192` exact 16:9 canvas with distortion-free 4:3 gameplay + docked 4:3 Map/Status sidebar), **16:9 Smart Crop** (`OVERSCAN` 1:1 square pixels + `-24..+24 px` vertical crop offset + exact touch mapping), and **16:10 Balanced**.
- **Native Opening Video Player:** optional Windows Media Foundation + SDL3 player for locally supplied `mods/intro/opening.mp4` (not bundled in clean-room releases).
- **Mod Architecture & Build Pipeline:** declarative `mods/registry.json`, 3-state `ConflictEngine` (`verified` / `unverified` / `incompatible`), isolated saves/savestates per `profile + version + modset_hash`, power-of-two cartridge padding for expanded ROMs, RecompLauncher `{data}` save-path guards, incremental RBS build/artifact verification (`Build.ps1`, `tools/rbs*.py`), and CI security/supply-chain workflows.

### Experimental / Unverified
- **Eternity of Sorrow 1.2 (Campaign):** boots and runs at ~60 FPS (`0` dispatch misses) with isolated saves; marked `unverified` pending a full playthrough from start to credits.
- **Albus Mode (Julius Mode Pack):** marked `unverified`; known inverted movement bug tracked in [Issue #19](https://github.com/Neguchads/cdos-nintendods-recomp/issues/19).
- **Model ZX / Model Omega (Julius Mode Packs):** marked `unverified`; boot-tested with isolated saves, full playthrough pending.
- **Native C++ Seed Mutator (Offline Randomizer):** mutates non-progression regular enemy stats/drops deterministically with isolated saves and spoiler log; full progression-checked item/boss randomization remains via the local **DSVania Randomizer (`dsvrandom`)** importer (`tools/import_dsvrandom.py`).
- **Widescreen Modes:** 2D presentation modes (Theatre / Smart Crop / 16:10); true ARM9 2D camera/tilemap wide-viewport decompilation remains future work (ADR-014).

## 1.0.1 (2026-11-23)

- Fix RecompLauncher startup paths, standalone launcher and mouse bindings. See [release notes](https://github.com/Neguchads/cdos-nintendods-recomp/releases/tag/v1.0.1).

## 1.0.0 (2026-11-22)

- Initial public Windows release.
