# CrownlteAudio: SYSMMU AUD CMU read-only snapshot

The tested-on-source diagnostic package is **not a compiled driver**. Use this as a text fallback if ChatGPT file downloads fail.

## Where it runs

In `Driver.c`, within `AudEvtPrepareHardware`, immediately after `AboxAudit(Dev)` and before `AudInspectCmu(Dev)`, insert:

```c
AudCmuSysmmuReadOnlySnapshot(Dev);
```

In `CrownlteAudio.h` add:

```c
VOID AudCmuSysmmuReadOnlySnapshot(_Inout_ PAUD_DEVICE_CONTEXT Dev);
```

At the end of `Exynos9810AudCmu.c`, after the existing `#include "CrownlteAudio.h"`, append this function (the existing C file already uses `READ_REGISTER_ULONG`):

```c
VOID AudCmuSysmmuReadOnlySnapshot(_Inout_ PAUD_DEVICE_CONTEXT Dev)
{
    volatile UCHAR *w;
    ULONG apb, qch, sysreg;
    if (!Dev || !Dev->CmuMapped || !Dev->Va[AUD_RES_CMU]) {
        if (Dev && Dev->Log)
            AudLog(Dev->Log, "AUD CMU SYSMMU snapshot skipped: no mapped CMU\r\n");
        return;
    }
    if (Dev->Len[AUD_RES_CMU] < 0x3064u + sizeof(ULONG)) {
        AudLog(Dev->Log, "AUD CMU SYSMMU snapshot skipped: short CMU window\r\n");
        return;
    }
    w = (volatile UCHAR *)Dev->Va[AUD_RES_CMU];
    apb    = READ_REGISTER_ULONG((volatile ULONG *)(w + 0x203Cu));
    qch    = READ_REGISTER_ULONG((volatile ULONG *)(w + 0x3060u));
    sysreg = READ_REGISTER_ULONG((volatile ULONG *)(w + 0x3064u));
    AudLog(Dev->Log, "AUD CMU SYSMMU snapshot read-only; AboxEnable=%lu\r\n", Dev->Cfg.AboxEnable);
    AudLog(Dev->Log, "AUD CMU SYSMMU_APB_GATE [0x203C] = %08lX (cg=%lu manual=%lu autogate=%lu)\r\n",
           apb, (apb >> 21) & 1u, (apb >> 20) & 1u, (apb >> 28) & 1u);
    AudLog(Dev->Log, "AUD CMU SYSMMU_QCH [0x3060] = %08lX (enable=%lu clock_req=%lu ignore_force_pm=%lu)\r\n",
           qch, qch & 1u, (qch >> 1) & 1u, (qch >> 2) & 1u);
    AudLog(Dev->Log, "AUD CMU SYSREG_QCH [0x3064] = %08lX\r\n", sysreg);
}
```

All three offsets are confirmed in Samsung `cal-if/exynos9810/cmucal-sfr.c` from the supplied kernel sources. **Do not read or write 0x7060 based on this reference**: the previously suggested SYSMMU QCH debug offset is not listed in that source.

Use `Debug|ARM64`, keep `AboxEnable=0`, `AmpInit=0`, `AmpEnable=0`. Avoid forced restart of this experimental audio device; a normal reboot is safer. Collect `C:\Windows\System32\LogFiles\CrownlteAudio-run.txt`.

The bitfields alone do not prove physical clock activity or authorization to write clock control registers. No hardware clock change is performed.
