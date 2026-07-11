# Decky LSFG-VK â€” Odin 2 Armada Fork

This is an unofficial Odin 2 Armada fork/package by **Janley**, based on
[Decky LSFG-VK](https://github.com/xXJSONDeruloXx/decky-lsfg-vk) v0.12.5.
It packages a native ARM64 `lsfg-vk` Vulkan layer that was verified on an
AYN Odin 2 Pro running Armada.

It is intended for native Linux games and games launched through Steam/Proton
on Armada. It is not an Android, Winlator, or GameNative package.

## Why this fork exists

The Decky Store version available during testing was v0.12.2. On Armada it
copied the x86-64 LSFG library before the installation stopped, leaving the
Vulkan manifest, configuration, and `~/lsfg` launcher missing.

Upstream Decky LSFG-VK v0.12.5 added native `aarch64` detection and uses the
actual home directory instead of assuming `/home/deck`, fixing the installer
path problem on Armada. Its original bundled ARM test binary still crashed
during Vulkan instance initialization on the tested Odin 2, however.

This fork therefore:

- starts from Decky LSFG-VK v0.12.5;
- keeps its `aarch64` detection and portable home-directory handling;
- replaces `bin/liblsfg-vk-arm64.so` with the Odin-tested ARM64 build;
- bundles that build directly instead of allowing Decky to fetch and replace
  it with the incompatible upstream ARM test binary;
- preserves the upstream Decky interface, configuration profiles, installer,
  uninstaller, and `~/lsfg` launch wrapper.

Patched ARM library SHA-256:

```text
d8247d5590dee7389b9636eea71d1ead054ce1fcf4b8c240cd8745a09241bddd
```

## Tested environment

- AYN Odin 2 Pro
- Armada / Fedora 44
- Linux kernel 7.0.11
- Snapdragon 8 Gen 2 / Adreno 740
- Turnip Mesa driver 26.1.2
- Vulkan loader 1.4.341
- Decky LSFG-VK base version 0.12.5

The layer was confirmed to:

- load as native `aarch64`;
- resolve all Linux runtime dependencies;
- discover the Vulkan implicit-layer manifest;
- find a legitimate Steam installation of `Lossless.dll`;
- extract the Lossless Scaling shaders successfully;
- initialize the Vulkan instance and device layers;
- create and recreate an LSFG swapchain context;
- remain active during a 20-second Gamescope/Turnip `vkcube` presentation
  test.

## Requirements

- An AYN Odin 2 family device running Armada Linux.
- Decky Loader.
- A legitimate Steam copy of Lossless Scaling.
- Lossless Scaling installed so that `Lossless.dll` is available to the
  plugin. This package does not contain the DLL or its proprietary shaders.

## Installation

1. Download the ZIP without extracting it.
2. Open Decky settings and enable **Developer Mode**.
3. Open the Decky **Developer** section.
4. Select **Install Plugin from ZIP** and choose this ZIP.
5. Open **Decky LSFG-VK** and select **Install** or **Reinstall LSFG-VK**.
6. Configure a profile. Start with 2Ã— and Performance Mode enabled.
7. Add this to the Steam launch options for each game:

   ```bash
   ~/lsfg %command%
   ```

The plugin automatically uses Armada's real home directory, normally
`/var/home/armada`. Do not replace it with a hard-coded `/home/deck` path.

## Verification

From Armada's terminal:

```bash
file ~/.local/lib/liblsfg-vk.so
vulkaninfo | grep -i lsfg
```

The first command should report `ARM aarch64`. The second should show:

```text
VK_LAYER_LS_frame_generation
```

Games must still be launched with `~/lsfg %command%` for the selected Decky
profile to apply.

The two core bundled binaries can also be checked from the extracted plugin
folder with:

```bash
sha256sum -c SHA256SUMS
```

## Known issue

During testing, a short `vkcube` run could segfault while the test application
was closing. A longer presentation run remained stable until deliberately
stopped. If a game works normally but crashes only while exiting, it may be
the same LSFG teardown issue. Runtime behavior can vary by game and Proton
version.

This is an early, unofficial community build and has not been validated on
every Odin 2 model or Armada image.

## Credits and fork chain

- **Odin 2 Armada adaptation and testing:** Janley
- **Decky LSFG-VK:** Kurt Himebauch / xXJSONDeruloXx
- **Original lsfg-vk:** PancakeTAS and contributors
- **ARM/Android fork used for the native ARM build:** FrankBarretta's
  `lsfg-vk-android`
- **Lossless Scaling:** THS; users must supply their own legitimately obtained
  `Lossless.dll`

The original Decky README is included as `README-UPSTREAM.md`. All original
copyright notices and licenses are retained in `LICENSE`.

This fork is not affiliated with or officially supported by AYN, Armada,
Decky Loader, Lossless Scaling, PancakeTAS, FrankBarretta, or the upstream
Decky LSFG-VK maintainers.
