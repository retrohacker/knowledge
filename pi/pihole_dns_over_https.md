# Locking Down DNS

This guide walks you through getting PiHole setup with DNS over HTTPS on a Raspberry Pi.

PiHole is like an ad-blocker for your entire network! It works by intercepting DNS requests (the thing that takes human readable domain names like `www.google.com` and turns them into ip addresses `172.217.164.110`) and refuses to "resolve them" (tell your computer the ip address) when the domain name is for an advertiser.

On top of this awesomeness, this guide will also ensure all of your DNS requests are _encrypted_. DNS was never really designed to have privacy by default. Because of this, anybody looking at your network traffic (i.e. Comcast, AT&T, your neighbor who figured out your wifi password, etc.) can see what websites you visit! What we are going to do is, instead of doing a real DNS request when the computers on your network try to resolve DNS values, we are going to instead do an HTTPS request (the thing websites use) to fetch the values instead. This encrypts all of your traffic, so only you and cloudflare will know what you are browsing.

It's worth noting that we are trusting cloudflare in this setup. It's possible they will abuse this privilage, [though we have pretty strong guarentees that they wont](https://1.1.1.1) since they have annual external audits.

So let's get started!

> _Note: this tutorial is written for Debian, if you are using another system you may need to modify some steps_

## Getting a Pi

Grab a [$25 Raspberry Pi 3 A+](https://www.adafruit.com/product/4027), a [5.2v power cord](https://www.adafruit.com/product/1995), and a [SD card](https://www.adafruit.com/product/1995) from Adafruit (or anywhere, but Adafruit is awesome and you should consider supporting them!).

If you do go with Adafruit, ordering the SD card from them too makes sense and is convienent. Though I have found some great deals on brand name SD cards on Amazon (i.e. 32GB Samsung SD card for $5.99). Worth looking there if you are wanting to buy multiple SD cards for other projects!

## Installing Raspbian

```
$ transmission-cli https://downloads.raspberrypi.org/raspbian_lite_latest.torrent
```

`ctrl-c` once the download finishes

```
$ unzip *-raspbian-*.zip
```

Download [etcher](https://www.balena.io/etcher/), select the downloaded .img file, select your SD card, and then flash it!

## Mucking with files before boot

This is needed for setting up wifi and ssh

```
$ mkdir pi_boot pi_rootfs
$ lsblk -f # identify the sdcard based on the device names
$ sudo mount /dev/sdb1 pi_boot
$ sudo mount /dev/sdb2 pi_rootfs
```

You can now edit the files on the pi directly from your computer!

When you are done editing:

```
$ sudo sync # super important, don't forget this!
$ sudo umount pi_boot
$ sudo umount pi_rootfs
```

## Setup WIFI and SSH

https://www.raspberrypi.org/documentation/configuration/wireless/headless.md

No need for WIFI if plugging directly into the router

Now boot up the pi. You will need to find it's IP address, the easiest way to do this is through your routers DHCP table, which is probably displayed in your router's UI as something like "connected devices"

## Configure Pi

```
$ sudo raspi-config
```

1) Set your locale
2) Change your password
3) Change hostname (I use `pihole`)

## SSH Keys

Probably best to use ssh key-pairs for ssh, if your system supports it, use [ed25519](https://wiki.archlinux.org/index.php/SSH_keys#Ed25519)

```
$ scp ~/.ssh/id_ed25519.pub pi@[ip_address]:/home/pi/.ssh/authorized_keys
```

## Static IP

At this point, you need to hop on your router and give your raspberrypi a static ip address.

## Setup PiHole

https://github.com/pi-hole/pi-hole/#one-step-automated-install

## Setup DNS over HTTPS

https://docs.pi-hole.net/guides/dns-over-https/

## Configure Router

At this point, you need to hop on your router and setup your raspberry pi as your *exclusive* upstream DNS server.
