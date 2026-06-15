# 🚀 AMDGPU 7.0.11 Stability Patch

**🎯 Better Stability & Recovery for Older AMD GPUs (CIK / Hawaii / GFX v7)**

This patch improves GPU reset handling, job recovery, and display stability on older Radeon cards.

---

## ✨ What's Fixed / Improved

### 1. **Display Resume After GPU Reset** 🖥️
- **Problem**: DCE (Display Engine) often failed during GPU reset on older hardware.
- **Solution**: Made DCE resume **non-fatal**. 
  - The GPU rings still recover properly.
  - Display may flicker or need re-init, but the system doesn't crash.
- **Affected file**: `amdgpu_device.c`

### 2. **VMID Reset Cleanup** 🧹
- Properly resets `pd_gpu_addr` when clearing VMIDs.
- Prevents potential stale page table issues after resets.

### 3. **Ring Timeout Recovery (Soft Recovery)** 🔄
- **Improved retry logic**: Now tries soft recovery **up to 3 times** with short delays.
- Much more reliable when the GPU temporarily hangs.
- **Affected file**: `amdgpu_job.c`

### 4. **GFX v7 Soft Recovery Enhancements** ⚡
- Kills stuck shader wavefronts in the affected VMID.
- **New**: If the Command Processor (CP) is hung, it now briefly halts and restarts ME/PFP/CE engines.
- This helps the ring make forward progress and signal fences even in tough cases.
- **Affected file**: `gfx_v7_0.c`

---

## 🎉 Benefits

- **Fewer hard crashes** after GPU hangs or resets
- **Better recovery** on older cards (Hawaii, Tonga, etc.)
- Improved user experience during heavy workloads or driver resets
- Display issues are now gracefully handled instead of killing the whole GPU session

---

## 📌 How to Apply

```bash
cd /path/to/kernel/drm/amd
patch -p1 < /path/to/amdgpu-7.0.11.patch
```

Then rebuild and reinstall the kernel module.

Kernel options:

```
radeon.si_support=0 amdgpu.si_support=1 amdgpu.dc=1 amdgpu.dpm=0 amdgpu.audio=0 amdgpu.gpu_recovery=1
```

---

## 🔧 Targeted Hardware
- **GFX v7.0 / CIK** (Hawaii, Bonaire, etc.)
- Older AMD Radeon GPUs using the amdgpu driver

---

**Made with ❤️ for better Linux AMD GPU stability**

*Tested on kernel 7.0.11 — June 2026*
