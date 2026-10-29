# Samsung Galaxy Note9 (crownlte) diagnostics

This branch holds source and download instructions for experimental Windows-on-ARM audio diagnostics on Exynos9810 / SM-N960N.

**Unverified hardware:** SYSMMU translation is disabled; this is not a working audio driver or a newly compiled UEFI image. The bootable UEFI image previously supplied is an existing build and does not contain the latest SYSMMU ACPI changes.

The diagnostic driver is **source**, requiring ARM64 Windows Driver Kit build and device-side testing.

Files are placed on this separate branch rather than changing `main`.
