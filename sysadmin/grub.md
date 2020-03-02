# Grub

Configuring grub on Debian is done through a set of files that are used to generate a full config:

* `/etc/default/grub` - Contains variables that control the generated config
* `/etc/grub.d/*` - The templates for generating the config, you can create files here to inject your own menu entries

### Super fast boot

If you want to skip the grub menu, set `GRUB_TIMEOUT=0` in `/etc/default/grub`.

To turn off disk checking, add `fastboot` to the end of `GRUB_CMDLINE_LINUX_DEFAULT` in `/etc/default/grub`
