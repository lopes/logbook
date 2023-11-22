+++
title = "Set Static IP Address in Arch Linux"
date  = 2021-10-27
description = "Learn how to set a static IP address in Arch Linux without DHCP support."

[taxonomies]
tags = ["arch", "unix", "networking"]
+++

In this post, I am going to explain how to set a static IP address in Arch Linux.  For the first time, I created a machine with Arch Linux without DHCP support, and although it is not a very difficult process, I took some notes and decided to publish them for further reference.

To install Arch Linux on this environment, it is needed to configure the network twice: One for the installation environment and another for the installed operating system.


## Installation Environment
Since the installation environment is volatile, there is no need to create any files, so the steps only include running commands.  First, you must find the network interface that will be configured and make sure it is up.  The next two commands do it.

```sh
ip address show
ip link set ens32 up  # assuming ens32 as the interface
```

Now, the IP address must be associated to that interface along with the network mask and broadcast address.  Arch Linux makes use of `iproute2` package to manage that and the next command does the job.  Note that the `broadcast +` parameter automatically calculates the broadcast address using the CIDR information right before it.

```sh
ip address add 10.0.1.2/24 broadcast + dev ens32
```

{% admonition(type="note", title="Note") %}
To remove an address from an interface, the command is similar: `ip address del 10.0.1.2/24 dev ens32`.
{% end %}

A default gateway must be informed to grant the connectivity.  The first of the next two commands lists the current routes and the second adds a default route `0.0.0.0/0` through gateway `10.0.1.1` associated to the same network interface.

```sh
ip route show
ip route add 0.0.0.0/0 via 10.0.1.1 dev ens32
```

{% admonition(type="note", title="Note") %}
To remove a route the command is almost the same, except for the parameter `del` that replaces the parameter `add`.
{% end %}

The last step is to adjust the DNS settings, but this live CD comes with the `systemd-resolved` (127.0.0.53) service configured, so there is nothing to do.  To get the status of this service, run the following command.

```sh
resolvectl status
```


## Permanent Installation
During the installation process, when the time comes to [configure the network settings](https://lopes.id/installing-arch-linux/#networking-1), create the file `/etc/systemd/network/20-wired.network` (assuming it is a wired or virtual interface) and fill it with the following content (change it according to your settings):

```
[Match]
Name=ens32

[Network]
Address=10.0.1.2/24
Gateway=10.0.1.1
DNS=1.1.1.1
DNS=1.0.0.1
```

Enable the `systemd-networkd` to make the network setting permanent and the `systemd-resolved` service to grant the name resolution:

```sh
systemctl enable systemd-networkd.service
systemctl enable systemd-resolved.service
```

Finish the Arch's installation process and after booting up the system, it should be connected to the internet.


## References
- [Arch Linux: Network Configuration](https://wiki.archlinux.org/title/Network_configuration)
- [Arch Linux: systemd-resolved](https://wiki.archlinux.org/title/Systemd-resolved)
