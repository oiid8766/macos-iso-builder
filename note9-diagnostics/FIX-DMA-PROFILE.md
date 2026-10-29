# CrownlteAudio ARM64 build fix (C2065)

In `CrownlteAudio-hardware-probe/Driver.c`, line 150, change:

```diff
- WDF_DMA_ENABLER_CONFIG_INIT(&DmaConfig, WdfDmaProfileScatterGather32,
+ WDF_DMA_ENABLER_CONFIG_INIT(&DmaConfig, WdfDmaProfileScatterGather,
                             0x01000000u);
```

`WdfDmaProfileScatterGather` is the documented 32-bit scatter/gather DMA profile.
Reference: https://learn.microsoft.com/en-us/windows-hardware/drivers/ddi/wdfdmaenabler/ne-wdfdmaenabler-_wdf_dma_profile

## Fix the existing local checkout using Windows PowerShell

```powershell
$path = "C:\Users\admin\Downloads\N9\exynos9810-woa-main\drivers\CrownlteAudio-hardware-probe\Driver.c"
$content = [System.IO.File]::ReadAllText($path)
if (-not $content.Contains("WdfDmaProfileScatterGather32")) { throw "No invalid enum found in Driver.c" }
[System.IO.File]::WriteAllText($path, $content.Replace("WdfDmaProfileScatterGather32", "WdfDmaProfileScatterGather"))
```

Rebuild ARM64 Debug. This change resolves only the undefined identifier; it does not validate hardware DMA, start SYSMMU translation, or fix speaker output.
