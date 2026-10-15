# Project 5: Building and Reaching the Linux Server

**Dates:** September 5 to 6, 2026

## Objective
Build an Ubuntu Server VM that shares a network with the domain controller, and set up reliable remote access to it.

## First install: what went wrong
The first install looked successful but was incomplete:

- The hostname was never applied.
- The `badmin` user account was never created, even though the setup screens had been completed.

I recovered through GRUB recovery mode and a root shell, confirmed with `ls /home` and `/etc/passwd` that the account did not exist, and created it by hand (`useradd`, `passwd`, `usermod -aG sudo`).

Logins then authenticated and immediately returned to the login prompt. `/var/log/auth.log` showed that `/etc/pam.d/login` referenced a missing `pam_lastlog.so` module, a known Ubuntu 24.04 issue (the module was replaced by `pam_lastlog2`). Commenting out the line did not fix the bounce-back. With several core steps already failing, I decided the base install was unreliable and rebuilt it instead of patching further.

## Clean reinstall
- Deleted the broken VM and created a new one, `ubuntu-lab`, on the same NAT Network as the domain controller.
- Went through the installer carefully: unattended install unchecked, hostname, username, and password checked on each screen, OpenSSH server enabled, optional snaps skipped.
- Verified the result directly: correct hostname in the prompt, first-try login, `sudo whoami` returned `root`, and an IP address on the same subnet as the domain controller.

The likely cause of the first failure was rushing the unattended-install and profile screens, which left a partially applied install. Checking that each setting actually applied was the lesson.

## Remote access
- Installed VirtualBox Guest Additions, but clipboard sharing still does not work on a text-only server console, because it depends on a graphical desktop.
- Switched to SSH instead. The first attempt timed out: a VirtualBox NAT Network allows VM-to-VM traffic but does not allow the host to reach a VM directly.
- Fixed it with a port-forwarding rule (host port 2222 to guest port 22) and connected with `ssh badmin@127.0.0.1 -p 2222`.

## Principles
- NAT, NAT Network, and port forwarding serve different purposes. Reaching a system from outside its network needs an explicit rule, a small-scale version of network access control.
- Verify that configuration really applied instead of trusting a wizard's success screen.
