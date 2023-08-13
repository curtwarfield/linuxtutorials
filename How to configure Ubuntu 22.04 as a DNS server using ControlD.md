# Configure Ubuntu 22.04 as a DNS server using ControlD

## Introduction
If you want to run your own DNS server for increased security, privacy, and control, using `ControlD` is a great way to accomplish this.

[ControlD](https://controld.com) is a fully customizable DNS service, similar to Pi-Hole, AdGuard or NextDNS, but with proxy capabilities. 

This means it not only blocks things (ads, porn, etc), but can also unblock websites and services.

## Prerequisites
* An active `ControlD` account.
* A `ControlD` **Resolver ID**.

## Installation

1. Install the `ctrld` utility.
~~~
sh -c 'sh -c "$(curl -sL https://api.controld.com/dl)" -s RESOLVER_ID_HERE'
~~~
Substitute `RESOLVER_ID_HERE` with your actual **Resolver ID**.

2. Start the `ctrld` service.
~~~
# ctrld start
~~~
3. Edit the `/etc/controld/ctrld.toml` file.
~~~
[listener]
  [listener.0]
    ip = '192.168.4.136'
    port = 53
~~~    