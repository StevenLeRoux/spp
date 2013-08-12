# SPP - SSH Proxy Platform

======

## Usage:

Usage: ssh {<opt>} <action>@<host> [<args>]

Available opt:
  -t            for ssh/tunnel
  -X or -Y      for vnc/rdp

Available actions:
  port
  rdp
  ssh
  tunnel
  vnc

Available Hosts:
  1.host.domain.tld
  2.host.domain.tld
  3.host.domain.tld
  4.host.domain.tld

## Actions

### SSH

To go to 1.host.domain.tld :
```
ssh -At ssh@spp 1.host.domain.tld
```

### Tunnel 

The `tunnel` action provide you a tunnel from your localhost to your destination through the SPP host.

You can either use it this way :

```
ssh -At tunnel@spp 1.host.domain.tld <port> <dynamic_port>
```

e.g. : 

```
ssh -At tunnel@spp 1.host.domain.tld 8080 30455
```

or w/ a more straightforward wrapper like this : 

```
sshtunnel 1.labs.arkea.com 8080
```

The wrapper handles the dynamic port negociation to manage multiple client tunnel to the same host/port. The wrapper basically just call `ssh port@spp getport` and immediatly mount the tunnel using this unallocated port.

Then the user just needs to join localhost:port.

 ___                       _____                        __________
|   |                     |     |                      |          |
| A | --- ssh tunnel ---> | SPP |  --- ssh tunnel ---> | 1.host...|
|___|                     |_____|                      |__________|

In the previous example, we have : 

A:8080    -> SPP:30455
SPP:30455 -> 1.host:8080
