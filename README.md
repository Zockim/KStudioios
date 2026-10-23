# HackiOS — XNU Kernel Workbench

An interactive workbench, hardware telemetry suite, and session runner for **HackiOS**: booting Apple's open-source **XNU kernel** (`xnu-8796.121.2`, the kernel of iOS 16.5) on Android phone hardware (POCO X5 Pro 5G / Snapdragon 778G `redwood`) and QEMU `virt` (aarch64).

## Features

- **Interactive Phone Simulator**: Authentic representation of the POCO X5 Pro 5G with curved display frame, punch-hole camera, status bar, hardware buttons (Power, Volume Rocker), home swipe indicator, and Control Center overlay.
- **Real Screen Captures Gallery**: 19 high-resolution captures of unmodified iOS 16.5 running on the phone:
  - Lock Screen, SpringBoard (Page 1 & 2), Settings, Clock, Calendar, Messages, Maps, Music, Wallet, Reminders, Search, Health, and App Store
  - GPU rendering diagnostics: Adreno SpringBoard rendering, OpenGL ES shader test, and first GPU frame capture
- **Session Runner & Boot Arguments Configurator**:
  - Device profile selection (POCO X5 Pro 5G vs QEMU virt)
  - Interactive toggles for `gpu=1` (Adreno 642L Mesa/freedreno), `wifi=1` (WCN6750 ath11k), `adsp=1` (audio DSP), `touchscreen=1` (Goodix GT9916), `cpufreq=1`, `var_store=1`, and `systrace=1`
  - Lifecycle actions replicating `./xl` (`enter`, `ramdisk`, `run`, `screenshot`, `reboot`)
- **Hardware & Driver Subsystems Matrix**:
  - Detailed bring-up status, driver files, and progress bars across 14 subsystems (CPU, GPU, Touch, Wi-Fi, Audio, Camera, APFS, Haptics, Hardware Keys, Metal shaders, Cellular, Bluetooth, Battery PMIC, and Secure Enclave)
- **Boot Chain & Architecture Deep Dive**:
  - 5-stage zero-flash boot chain (`fastboot boot` → Mu-Silicium UEFI → xnuloader EFI → XNU 8796.121.2 → iOS 16.5 launchd/SpringBoard)
  - Driver replacement matrix comparing Apple closed drivers with HackiOS open-source shims
  - Snapdragon 778G physical memory map layout
- **Device Porting Guide & Checks**:
  - Automated device profile wizard for porting to any Android device
  - Diagnostic traps and solutions from `docs/checks.md`
- **Interactive Terminal Console**:
  - Real-time XNU kernel log ring buffer with level filtering, copy, export, and interactive `xl` CLI shell

## Architecture

```
fastboot boot ──► UEFI (Mu-Silicium) ──► xnuloader (EFI application)
                                           │  resident at 0x9FD00000 on Core 0
                                           │  fastboot USB protocol link
                                           ▼
            XNU (BCM2837 ARM64 base + redwood Qualcomm platform)
              ├─ GICv3 distributor, ARM64 generic timer, PSCI SMP
              ├─ APFS ramdisk /dev/md0 on /, /dev/md1 on /redwood
              ├─ Open replacements: MiniIOSurface, MiniIOMobileFramebuffer, MiniAppleKeyStore
              ├─ Hardware: Adreno 642L GPU, Goodix GT9916 touch, WCN6750 Wi-Fi, ADSP audio
              ▼
   iOS 16.5 launchd → notifyd, cfprefsd, backboardd, SpringBoard, stock apps
```

## Running the Application

```sh
npm install
npm run dev
```

Built with React 18, Vite, TypeScript, Tailwind CSS, and Lucide Icons.
