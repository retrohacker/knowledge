To create a tunnel over SSH to rsync betleen two hosts through an intermediary server (assumes debian):

Laptop A is the source
Laptop B is the intermediary

Setup a server (i.e. on digital ocean) to act as an intermediary (password auth will be easiest for a throwaway server)

Then, on Laptop A, ensure ssh and rsync are installed:

```sh
sudo apt install openssh-server rsync
```

Then allow connecting via password in `/etc/ssh/sshd_config`:

```txt
PasswordAuthentication yes
```

Now on the intermediary server, edit `/etc/ssh/sshd_config`:

```txt
GatewayPorts clientspecified
```
Now on Laptop A:

```sh
ssh -R 0.0.0.0:2222:localhost:22 root@[IP]
```

This opens up a tunnel to Laptop A through the intermediary.

Now on Laptop B:

```sh
sudo apt install rsync
rsync -avz --progress -e "ssh -p 2222" [LAPTOP_A_USER]@[INTERMEDIARY_IP]:[REMOTE_PATH] [LOCAL_PATH]
```
