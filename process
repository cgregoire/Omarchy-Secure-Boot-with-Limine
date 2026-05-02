# Secure Boot with Limine (Omarchy/Arch)

Guide for enabling UEFI Secure Boot on Omarchy using Limine
(>= 11.2.0) and sbctl. Tested on a dual-disk setup (Linux + Windows on
separate M.2 drives).

## Prerequisites

- Limine >= 11.2.0 (strict secure boot enforcement)
- sbctl installed (`yay -S sbctl`)

## 1. Enter Setup Mode in UEFI

Reboot into firmware settings:

In the BIOS, find the Secure Boot section and **reset to Setup Mode** (or
"restore factory keys" then clear them). Save and boot back into Linux.

## 2. Create and enroll keys

```bash
sudo sbctl create-keys
sudo sbctl enroll-keys --microsoft --firmware-builtin
```

The `--microsoft` flag keeps Microsoft's keys so Windows can still boot.
The `--firmware-builtin` flag preserves OEM firmware keys.

Verify:

```bash
sudo sbctl status
```

Expected: `Setup Mode: Disabled`, `Secure Boot: Disabled` (not yet enabled),
`Vendor Keys: microsoft`.

## 3. Enable Limine config checksum enrollment

Edit `/etc/default/limine` and add:

```
ENABLE_ENROLL_LIMINE_CONFIG=yes
```

## 4. Enroll config checksum and update Limine

```bash
sudo limine-enroll-config
sudo limine-update
```

`limine-update` rebuilds the UKI, signs it via the sbctl pacman hook, hashes
the config, and enrolls the checksum into the Limine EFI binary.

## 5. Sign all EFI binaries

Sign each EFI binary that sbctl detects. The `-s` flag saves the file to
sbctl's database so future updates are re-signed automatically.

```bash
sudo sbctl verify
```

Sign anything marked as not signed:

```bash
sudo sbctl sign -s /boot/EFI/BOOT/BOOTX64.EFI
sudo sbctl sign -s /boot/EFI/limine/limine_x64.efi
sudo sbctl sign -s /boot/EFI/limine/BOOTX64.EFI
# Add any other unsigned files shown by sbctl verify
```

## 6. Verify everything

```bash
sudo sbctl verify
```

All files should show `is signed` with no `incorrect digest` errors.

## 7. Enable Secure Boot

Reboot into firmware settings

Enable Secure Boot in the BIOS, save, and boot normally.

## 8. Confirm

```bash
sudo sbctl status
```

Expected: `Secure Boot: Enabled`.
