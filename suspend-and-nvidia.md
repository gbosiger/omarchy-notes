# Omarchy Suspend and NVIDIA Notes

Last checked: 2026-09-17

## Hardware

- Alienware m15 R4, BIOS 1.24.0 dated 2024-08-09.
- NVIDIA GeForce RTX 3070 Laptop GPU.
- Omarchy graphics mode must remain `Hybrid`; USB-C display output depends on the NVIDIA path.
- External monitor: ChangHong PMO S323-IQSC, connected through the NVIDIA/USB-C display path.

## Current intentional settings

`/etc/default/limine` and `/etc/kernel/cmdline` include:

```text
nvidia_drm.modeset=1 nvidia_drm.fbdev=1
```

`/etc/modprobe.d/nvidia.conf` contains:

```text
options nvidia_drm modeset=1
options nvidia NVreg_PreserveVideoMemoryAllocations=1
options nvidia NVreg_TemporaryFilePath=/var/tmp
```

These settings require regenerated Limine UKIs after changes:

```bash
sudo mkinitcpio -P
sudo limine-update
```

The NVIDIA suspend hooks are enabled:

```text
nvidia-suspend.service
nvidia-resume.service
```

Verify them with:

```bash
systemctl is-enabled nvidia-suspend.service nvidia-resume.service
cat /proc/driver/nvidia/params | rg 'PreserveVideoMemory|TemporaryFilePath'
```

## Findings

- The system successfully leaves suspend, but the graphical session can fail to recover.
- With the USB-C display connected, resume produced NVIDIA semaphore errors, Thunderbolt xHCI resume errors, and USB-C/UCSI failures.
- The laptop resumed successfully when the USB-C display cable was unplugged before suspend.
- Turning the monitor off is not equivalent to unplugging USB-C; the physical USB-C/DisplayPort link remains present.
- The internal laptop panel is normally disabled, so a failed external-display resume can look like a complete system hang.
- `nvidia_drm.modeset=1`, framebuffer mode, and NVIDIA suspend/resume hooks did not fix the USB-C-connected failure.

## Practical workaround

Unplug the USB-C display connection before suspending, then reconnect it after wake. HDMI is a useful A/B test because it bypasses the USB-C/Thunderbolt controller, although it still uses NVIDIA external-display handling.

## After updates or snapshot restores

Check that these files still contain the intentional settings:

```bash
grep -n 'nvidia_drm\|PreserveVideoMemory\|TemporaryFilePath' \
  /etc/default/limine /etc/kernel/cmdline /etc/modprobe.d/nvidia.conf
systemctl is-enabled nvidia-suspend.service nvidia-resume.service
supergfxctl -g
```

Expected graphics mode is `Hybrid`.

If boot arguments changed, rebuild the UKIs with `mkinitcpio -P` and `limine-update`, then reboot. Do not edit `/etc/modprobe.d/supergfxd.conf`; `supergfxd` generates that file.

## Firmware and package history

- `fwupd` and `fwupd-efi` were installed.
- LVFS reported no available BIOS/System Firmware update for this laptop.
- A non-BIOS firmware update was installed successfully.
- Omarchy updates completed package migrations; kernel and NVIDIA versions remained unchanged at the time of testing.

## Reconsideration

If the extra NVIDIA suspend settings cause unwanted delay, storage use, or new behavior, compare against stock settings. They are not required for normal NVIDIA operation and did not resolve the USB-C resume failure. The USB-C/Thunderbolt path remains the primary suspect.
