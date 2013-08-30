# SPP - SSH Proxy Platform

SPP is a defined as an SSH Proxy Platform to provide an access through a `bounce Host` without any shell access. By default, a user is granted by his SSH key deployed in authorized_keys and has access to a defined list of Host, but you can change these and put some glue to inherit some rule from a CMDB or a user database.

The goal is resumed like this : 

```
                           ___
User 1 (Network  A)  ---> |   | ---> Network ...
                          |   | ---> Host n
User 2 (Network ...) ---> |SPP| ---> Routers
                          |   | ---> Switches
User n (Network  Z)  ---> |___| ---> ...

```

It becomes easier for network/access policies team to manage SSH and host access through their networks : 

```
ANY:ANY -> spp:22
SPP:ANY -> ANY:22
```
SSH access is not anymore host/network related. SPP provides a by-user access policies, simply based on SSH-KEYS and SSH-AGENT features.

## Usage:
```
Usage: ssh {<opt>} <action>@<host> [<args>]

Available opt:
  -t            for ssh/tunnel
  -X or -Y      for vnc/rdp

Available actions:
  rdp
  ssh
  tunnel
  vnc

Available help:
  For each <action> : ssh <action>@spp help


Available Hosts:
  1.host.domain.tld
  2.host.domain.tld
  3.host.domain.tld
  4.host.domain.tld
```

## Actions

### SSH

To go to 1.host.domain.tld :
```
ssh -At ssh@spp 1.host.domain.tld
```
#### Client wrapper

`sshp <HOST>`

e.g. :

`sshp 1.host.domain.tld`

### Tunnel 

The `tunnel` action provides you a tunnel from your localhost to your destination through the SPP host. It's useful for devs or users who need to address locally a backend/database/... but using an SSH tunnel to access the service on a given Host.

You can either use it this way :

```
ssh -At tunnel@spp <HOST> <PORT> <DYNAMIC_PORT>
```

e.g. : 

```
ssh -At tunnel@spp 1.host.domain.tld 8080 30455
```

#### Client wrapper :

This wrapper provide a more straightforward way of use : 

```
sshtunnel 1.labs.arkea.com 8080
```

The wrapper handles the dynamic port negociation to manage multiple client tunnel to the same host/port. The wrapper basically just call `ssh port@spp getport` and immediatly mount the tunnel using this unallocated port.

Then the user just needs to join localhost:port.
```
 ___                       _____                        __________
|   |                     |     |                      |          |
| A | --- ssh tunnel ---> | SPP |  --- ssh tunnel ---> | 1.host...|
|___|                     |_____|                      |__________|
```
In the previous example, we have : 
```
A:8080    -> SPP:30455
SPP:30455 -> 1.host:8080
```

## Install

Just git clone in your prefered directory (e.g. /opt/spp) so that you have :

```
(/opt/spp)/bin
          /bin/spp
          /etc
          /etc/host/
          /etc/host/[host list]
          /wrappers
          /wrappers/sshp
          /wrappers/sshtunnel
```

* Now create a user for each `action` you want to provide (e.g. ssh/tunnel/port/vnc/rdp/...)
* Deploy ssh keys for the user you want to grant on a given `action`.
* Adjust the Host list the user group can access. ( etc/hosts/...)
* Modify the `/etc/passwd` file to replace the default shell (seventh field) by the SPP binary (e.g. /opt/spp/bin/spp)

You're good to go.

## Deployment tips

* Make an LXC container to bring all the rules you want isolated a bit more between user groups. This way you can have a different @bouce_host_ip, one for sysadmins, one for network team, one for devs... but give them the same actions (ssh@devs, ssh@network, ssh@dba, ...)

## Todo list
 * Improve user fine management (instead of default root user for the target host)
