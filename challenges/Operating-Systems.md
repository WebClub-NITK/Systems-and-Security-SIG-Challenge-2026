# Full Disk Encryption from Scratch: Manual LUKS + TPM2 Autounlock on a Live Ubuntu Install

## Introduction

When you install Ubuntu normally, there's a checkbox for "encrypt my
disk." This task is about understanding what that checkbox actually
does, by doing it yourself, by hand, step by step — using a USB live
environment instead of the normal installer.

You'll install Ubuntu with no encryption first, make sure it boots, and
only then encrypt it. After that, you'll set it up so your TPM chip
(a small security chip most modern PCs have) can unlock the disk
automatically, without you typing a password every boot.

---

## Problem Statement

Here's the order you'll do things in:

1. Partition the disk and install a plain, unencrypted Ubuntu system.
2. Make sure it boots on its own.
3. Encrypt the root partition *after the fact*, without wiping it.
4. Fix the bootloader so it knows to ask for a password before booting.
5. Set up the TPM chip so the disk unlocks automatically — but keep the
   password as a backup in case the automatic unlock stops working.

Secure Boot needs to stay turned on the whole time. You don't need to
understand everything about it yet — just enough to know when you'd
need to sign something yourself (spoiler: for this task, you mostly
won't).

The goal isn't just "make it boot." It's understanding *why* each step
is needed, so if something breaks, you know what to check.

---

## Step 1: Partition and Install Ubuntu

Boot from the USB. Create two partitions on the disk:
- A small one for the bootloader (EFI System Partition)
- A bigger one for everything else (root partition)

Use a tool called `debootstrap` to install a minimal Ubuntu system onto
the root partition. Then set it up with a kernel, GRUB (the
bootloader), and basic system config — hostname, password, etc.

Install the bootloader, reboot off the disk (not the USB), and make
sure it boots into a working, unencrypted Ubuntu. Confirm Secure Boot
is still happy with it.

## Step 2: Encrypt the Root Partition In Place

This is the part a normal installer hides from you: turning an
already-installed system into an encrypted one, without starting over.

Boot back into the USB. Don't touch the root partition yet. Use
`cryptsetup reencrypt --encrypt` to convert it to LUKS2 encryption
*while keeping all the files on it*. This step takes a while and is the
riskiest part — it can resume if interrupted, but you should understand
why before you run it, not after something goes wrong.

If there isn't enough free space on the partition for the encryption
header, you'll need to shrink the filesystem first with `resize2fs`,
then grow it back once encryption is done.

## Step 3: Fix the Boot Process

An encrypted partition won't boot on its own — the system needs to know
to ask for a password *before* it tries to load anything from that
partition.

Mount the now-encrypted partition, and update three files:
- `/etc/crypttab` — tells the system this partition is encrypted
- `/etc/fstab` — points root at the new encrypted device
- GRUB's config — tells the bootloader to prompt for a password

Rebuild the boot files and reinstall GRUB. Reboot and confirm it now
asks for a password and boots correctly.

## Step 4: Set Up MOK (Secure Boot Key Enrollment)

You probably won't need this yet, but it's good to set up now. MOK
("Machine Owner Key") lets you sign your own files so Secure Boot
trusts them — needed later if you ever install something like a custom
driver.

Generate a key, enroll it using `mokutil`, and confirm it through the
blue MOK Manager screen that appears on your next reboot. Nothing
you've built so far actually needs this key yet — you're just getting
it ready.

## Step 5: Set Up Automatic Unlock with TPM2

Once password-based boot is working reliably, set up the TPM chip to
unlock the disk automatically — no password needed on a normal boot.

Use a tool like `clevis` to add a second way to unlock the disk,
sealed to your TPM. This works *alongside* your password, not instead
of it — keep the password as a backup.

Rebuild the boot files again, then reboot and confirm it unlocks with
no prompt at all.

**Important:** this automatic unlock can break on its own — a firmware
update or BIOS change can make the TPM refuse to unlock. That's exactly
why you keep the password. Test this yourself: change a BIOS setting on
purpose, reboot, and confirm it falls back to asking for your password
instead of failing completely.

---

## Deliverable

A machine that boots from your installed disk, passes Secure Boot
checks, and unlocks itself automatically using the TPM — but still asks
for your password if anything about the boot process changes.

Write up:
- Your final partition layout and UUIDs
- Which TPM settings (PCRs) you used for auto-unlock
- Anything that went wrong during encryption, and how you'd fix it

## Resources

- [LUKS2 Format Overview](https://gitlab.com/cryptsetup/LUKS2-docs)
- [cryptsetup reencrypt docs](https://man.archlinux.org/man/cryptsetup-reencrypt.8)
- [debootstrap guide](https://wiki.debian.org/Debootstrap)
- [Arch Wiki: Encrypting a system with LUKS](https://wiki.archlinux.org/title/Dm-crypt/Encrypting_an_entire_system)
- [Clevis + TPM2 docs](https://github.com/latchset/clevis)
- [Secure Boot and MOK explained](https://wiki.ubuntu.com/UEFI/SecureBoot)


