If, after connecting to a vpn or a wifi hotspot, you find that DNS stops working the config is probably changing your `/etc/resolv.conf` file.

First, open the `/etc/resolv.conf` file and replace its contents with the following:

```
nameserver 8.8.8.8
nameserver 8.8.4.4
```

Next run `sudo chattr +i /etc/resolv.conf`. This sets the immutable flag on the file preventing it from being changed in the future.

Finally, run `sudo service network restart`. You should be able to connect to the network or vpn now without loosing DNS.
