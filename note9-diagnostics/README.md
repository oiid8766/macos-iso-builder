# Galaxy Note9 / crownlte diagnostic files

This branch is reserved for experimental Exynos9810 Windows-on-ARM audio diagnostics. It does **not** contain the binary downloads yet.

Files to upload from the earlier ChatGPT session:

- `Note9_Diagnostics_20261029.zip` — complete archive: `uefi-existing.img`, `CrownlteAudio-diagnostics-source.zip`, `UEFI-SYSMMU-source.zip`.
- `Note9_Driver_Source_20261029.zip` — standalone driver source.

These files exist in the ChatGPT working environment, but the available GitHub integration cannot directly import local binary files from that environment. **Do not treat this branch as a usable download until the archives are actually uploaded.**

The UEFI image is an existing build, not rebuilt with the newest SYSMMU ACPI resource. The diagnostic driver is source only, not a compiled `.sys` file. SYSMMU translation remains disabled and hardware operation is unverified.

Suggested upload destination: a GitHub Release under this repository, which preserves the macOS project main branch and supports binary release assets.
