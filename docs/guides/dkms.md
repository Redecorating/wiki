# Introduction

This page explains how to install the kernel modules for the Keyboard, Audio, Touchbar, and the Ambient Light sensor with DKMS. You will need a patched kernel. If you have updated from a kernel like 5.7 to 5.10, you will need the version of the Touchbar module linked to in this guide, as a function older versions of the module rely on was removed.

# Do you need to do this?

Is your keyboard working? If no, then you'll need the BCE module. If you have a Touchbar, is it working? If no, then you'll need the apple-ibridge module.

# Installing modules

1. Install the `dkms` package
2. Installing the BCE (Buffer Copy Engine) module for Keyboard and Audio
	- If you are on arch, get Aunali1's [apple-bce-dkms-git package](https://github.com/aunali1/apple-bce-arch/releases), which is also on the `[mbp]` pacman repo.
	- Otherwise, `sudo git clone https://github.com/t2linux/apple-bce-drv /usr/src/apple-bce-r183.c884d9c`
	- Use `sudo dkms install -m apple-bce -v r183.c884d9c`. Add `-k x.x.x-mbp` if you need to install for a specific kernel version (you'll probably need to specify this if you booted from an install ISO).
3. Installing the Touchbar and Ambient Light sensor modules
	- `sudo git clone https://github.com/t2linux/apple-ib-drv /usr/src/apple-ibridge-0.1`
	- Use `sudo dkms install -m apple-ibridge -v 0.1`. Add `-k x.x.x-mbp` if you need to install for a specific kernel version.
4. Load the modules into the kernel

```
sudo modprobe apple_bce
sudo modprobe apple_ib_tb
sudo modprobe apple_ib_als
```

The Touchbar and keyboard should work, for audio, you'll need some config files, refer to the [Audio Config guide](https://wiki.t2linux.org/guides/audio-config).

# Fixing Suspend

Copy [this script](https://github.com/marcosfad/mbp-ubuntu/blob/master/files/suspend/rmmod_tb.sh) to `/lib/systemd/system-sleep/rmmod_tb.sh`

It unloads the Touchbar modules as they can cause issues for suspend.

# Issues

The `apple_ib_als` module can cause issues, if you find your computer hanging at shutdown, or having BCE errors at boot, try blacklisting it `sudo sh -c "echo blacklist apple-ib-als" >> /etc/modprobe.d/blacklist.conf`
