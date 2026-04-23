# 1. Add disk decrypt using TPM2.0
## Background
Starting with `Omarchy 2.0`, ISO downloads became available, but they enforce full-disk encryption by default.
Because of this, it became necessary to enable automatic decryption after reboot.

## Pre-requisites
Make sure TPM2.0 is enabled in BIOS > Secure chip 

## How to Enable clevis with TPM2 on Omarchy

### Install clevis and required tools
```
sudo pacman -S --needed clevis tpm2-tools tpm2-tss
yay -S --needed mkinitcpio-clevis-hook
```
# Detect encrypted disk
```
lsblk -f
```

Sample lsblk output:
Find the disk where `FSTYPE` is `crypto*`. (In this case, it's `/dev/nvme0n1p2`.)
```
NAME        FSTYPE      FSVER LABEL UUID                                 FSAVAIL FSUSE% MOUNTPOINTS
zram0       swap        1     zram0 699562fd-eae9-44c9-afbd-184644b5d14a                [SWAP]
nvme0n1
├─nvme0n1p1 vfat        FAT32       9810-60B6                               1.7G    12% /boot
└─nvme0n1p2 crypto_LUKS 2           b9d1af4d-311e-476b-a3e2-555a6642dc90
  └─root    btrfs                   4b1f95c8-3f71-48bd-b353-78d68cd70dca  213.7G     9% /var/cache/pacman/pkg
                                                                                        /home
                                                                                        /var/log
                                                                                        /
```
## Bind LUKS with TPM2
```
sudo clevis luks bind -d /dev/nvme0n1p2 tpm2 '{}'
```
### Verify
```
sudo clevis luks list -d /dev/nvme0n1p2
```
```
1: tpm2 '{"hash":"sha256","key":"ecc"}'
```

## Add clevis hook
```
sudo sed -i -E '/^HOOKS=.*\bclevis\b/! s/^(HOOKS=\([^)]*)\bencrypt/\1 clevis encrypt/' /etc/mkinitcpio.conf.d/omarchy_hooks.conf
```

## Verify configuration
```
grep -E '^HOOKS=' /etc/mkinitcpio.conf.d/omarchy_hooks.conf
```
```
# ⇒ HOOKS=(... clevis encrypt ...)
HOOKS=(base udev plymouth keyboard autodetect microcode modconf kms keymap consolefont block  clevis encrypt filesystems fsck btrfs-overlayfs)
```
# Regenerate initramfs
```
sudo mkinitcpio -P
```
## Verify clevis modules are included
```
lsinitcpio -a /boot/initramfs-linux.img | grep -Ei '(clevis|tpm2|tss2|jose)'
```
## Reboot to apply changes
```
reboot
```

If you are not prompted for a password after restarting, it was successful.

# 2. Enable hyperlock at bootup
## Background
Now that TPM unlocks disk, there is no user login that is stopping for anyone to access the device. So, we will lock hyprland with hyprlock

Edit your Hyprland config:
```
nvim ~/.config/hypr/hyprland.conf
```
Add:
```
exec-once = hyprlock
```

## Optional
Also enable auto-lock on resume: 
Add in hypridle.conf:
```
listener {
    timeout = 0
    on-timeout = hyprlock
}
```
NOTE: DID NOT DO THIS.

# 3. Control Screensaver Animations
I found the matrix effect by omarchy config with tte (text terminal effects) very cool, out of all random effects tte provides. 
So I did this in `.local/share/omarchy/bin/omarchy-cmd-screensaver`
```
  ...
  # Commented this out on 12/April/2026 for matrix efffect only
  #tte -i ~/.config/omarchy/branding/screensaver.txt \
  #  --frame-rate 120 --canvas-width 0 --canvas-height 0 --reuse-canvas --anchor-canvas c --anchor-text c\
  #  --random-effect --exclude-effects dev_worm \
  #  --no-eol --no-restore-cursor &

  # Added this on 12/April/2026 for matrix effect
  tte -i ~/.config/omarchy/branding/screensaver.txt \
    --frame-rate 120 --canvas-width 0 --canvas-height 0 --reuse-canvas --anchor-canvas c --anchor-text c\
    --random-effect --include-effects matrix \
    --no-eol --no-restore-cursor &
  ...
```

# 4. Changing logo (WIP)
Logo for disk logon screen:
```
/usr/share/sddm/themes/omarchy/logo.svg
```

Logo from theme (?):
```
/usr/share/plymouth/themes/omarchy/logo.png
```






 
