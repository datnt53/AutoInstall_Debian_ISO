# Guideline to build Custom ISO for Auto-install Debian Distro

## Create a directory to mount your source

```
mkdir /tmp/bootiso
```

## Loop mount the source ISO you are modifying (Download from Ubuntu)

```
mount -o loop /path/to/some.iso /tmp/bootiso
```

## Create a working directory for your customized media
```
mkdir /tmp/bootisoks
```

## Copy the source media to the working directory
```
cp -rT /tmp/bootiso/* /tmp/bootisoks/
```

## Change permissions on the working directory
```
chmod -R u+w /tmp/bootisoks
```

## Copy your Kickstart script which has been modified for the packages to the working directory [Reference](https://github.com/datnt53/AutoInstall_Debian_ISO/blob/master/ks.cfg)
```
cp /path/to/ks.cfg /tmp/bootisoks/ks.cfg
```

## Creating preseed file to automate some tasks [Reference](https://github.com/datnt53/AutoInstall_Debian_ISO/blob/master/custom.preseed)
Some of preseed template is [here](https://github.com/datnt53/AutoInstall_Debian_ISO/blob/master/preseed.template) 
```
cp /path/to/custom.preseed /tmp/bootisoks/custom.preseed
```

## Modifying grub.conf to load preseed file and kickstart file [Reference](https://github.com/datnt53/AutoInstall_Debian_ISO/blob/master/grub.cfg)
**Directory:**`/tmp/bootisoks/boot/grub/grub.cfg`
```
set timeout=30 --> Changing this value to decrease or increase timeout (second)
menuentry "Install Ubuntu Server" {
	set gfxpayload=keep
	linux	/install/vmlinuz  file=/cdrom/preseed/ubuntu-server.seed quiet ---
	initrd	/install/initrd.gz
}
```
--> Changing line:
```
linux	/install/vmlinuz  file=/cdrom/preseed/ubuntu-server.seed quiet ---
```
TO:
```
linux	/install/vmlinuz file=/cdrom/custom.preseed quite ks=cdrom:/ks.cfg ---
```

## Re-building ISO file with UEFI boot
```
mkisofs -U -A "Custom1804" -V "Custom1804" -volset "Custom1804" -J -joliet-long -r -v -T -o /tmp/autoinstall-ubuntu-18.04.4-server-amd64.iso -b isolinux/isolinux.bin -c isolinux/boot.cat -no-emul-boot -boot-load-size 4 -boot-info-table -eltorito-alt-boot -e boot/grub/efi.img -no-emul-boot /tmp/bootisoks

-o /tmp/autoinstall-ubuntu-18.04.4-server-amd64.iso: Writing output file to this full-directory.
/tmp/bootisoks: Working directory
```
**NOTES:** Installing `mkisofs` command
```
sudo apt-get install -y mkisofs
or
sudo apt-get install -y genisoimage
```