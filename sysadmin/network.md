# Networking under Debian

Systemd manages network configuration on interfaces

To have systemd manage a network configuration for you, create the file
`/etc/systemd/network`:

```
[Match]
# Which network interfaces this applies to

[Network]
# How to configure the network
```

For a full list of what can go in these section, checkout `man systemd.network`

# Example

This one config should handle the 99% case:

`ip a` - look at the output of this command to find your wired network card

Then, for your config file (using `enps01` as an example interface):

```
[Match]
Name=en*

[Network]
DHCP=yes
```

This matches all network interfaces beginning with `en` and configures them to
use DHCP.

Then `systemctl restart systemd-networkd.service`. That's it!

Typing `networkctl` should now show that the interface is managed. Networkd will
now automagically configure your network whenever you plug an ethernet cable
in.
