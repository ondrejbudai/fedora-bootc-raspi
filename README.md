# Fedora bootc image for Raspberry Pi

> This is experimental at this moment. Horrible hacks are included.

This is a bootc-powered Fedora base image compatible with Raspberry Pi. See
https://docs.fedoraproject.org/en-US/bootc/ for more information about bootc and Fedora.

The main difference between the base Fedora image and this repository is that this repository's Containerfile patches
bootupd so it can handle Raspberry Pi-specific bootloader and firmware files under /boot/efi that are needed
to successfully boot a Pi. See https://github.com/coreos/bootupd/issues/651 for more information about what's needed
in bootupd.

## Use

Clone this repository on an aarch64 machine and run:

```
sudo podman build -t localhost/fedora-bootc-raspi:40 .
mkdir output
sudo podman run \
  --rm \
  -it \
  --privileged \
  --pull=newer \
   --security-opt label=type:unconfined_t \
   -v $(pwd)/output:/output \
   -v /var/lib/containers/storage:/var/lib/containers/storage \
   quay.io/centos-bootc/bootc-image-builder:latest \
   --type raw \
   --local \
   --rootfs ext4 \
   localhost/fedora-bootc-raspi:40
# arm-image-installer can deal only deal with xz archives, let's deal with that
xz -v -0 -T0 output/raw/disk.raw
# substitute /dev/sda with your root disk
sudo arm-image-installer --target=rpi4 --media=/dev/sda --image output/raw/disk.raw.xz --resizefs
```

If you want a user inside the image, add `-v $(pwd)/config.toml:/config.toml` to `podman-run` and create the following blueprint in `config.toml`:
```
[[customizations.user]]
name = "alice"
password = "$6$..."
key = "ssh-ed25519 AAAA..."
groups = ["wheel"]
```

Alternatively, just add a layer with a user.

## Known issues
The horrible bootupd hack currently cannot update the Raspberry Pi specific firmware and bootloader. This means that
you are stuck with their versions from the initial disk image, unless you manually update them.
