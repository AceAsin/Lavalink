# [Lavalink](https://github.com/freyacodes/Lavalink)

Helpful commands and documentation to properly get Lavalink to run on Ubuntu 18/20 (LTS). It's also possible to run Lavalink with Ubuntu 16 (LTS) and Debian 9/10, but you might encounter a few issues. All zeros for the tunnel must be replaced with the appropriate addresses when setting up a route planner. These can either be the **Server IPv4 Address**, **Client IPv4 Address**, or **Routed /48**.

## Information

```txt
   .   _                  _ _       _    __ _ _
  /\\ | | __ ___   ____ _| (_)_ __ | | __\ \ \ \
 ( ( )| |/ _` \ \ / / _` | | | '_ \| |/ / \ \ \ \
  \\/ | | (_| |\ V / (_| | | | | | |   <   ) ) ) )
   '  |_|\__,_| \_/ \__,_|_|_|_| |_|_|\_\ / / / /
=========================================/_/_/_/

Version:        8ac9b9576910aabff7a683195cb1cd904bb59ee8-SNAPSHOT
Build:          1337
Build time:     26.01.2022 11:01:52 UTC
Branch          dev
Commit:         8ac9b95
Commit time:    26.01.2022 10:58:41 UTC
JVM:            13
Lavaplayer      1.3.97-original
```

## Setup

### [Node Version Manager](https://github.com/nvm-sh/nvm)

```sh
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.38.0/install.sh | bash
```

```sh
wget -qO- https://raw.githubusercontent.com/nvm-sh/nvm/v0.38.0/install.sh | bash
```

```sh
nvm i --lts
```

### [Java Runtime Environment](https://jdk.java.net/) & [Java Development Kit](https://jdk.java.net/)

```sh
sudo apt update
```

```sh
sudo apt install openjdk-13-jre-headless
```

```sh
sudo apt install openjdk-13-jdk-headless
```

```sh
sudo apt upgrade
```

### [Project Reposity](https://github.com/AceAsin/Lavalink)

```sh
git clone https://github.com/AceAsin/Lavalink.git
```

### [Node Package Manager](https://github.com/npm/cli)

```sh
npm i pm2 --g
```

```sh
npm run build
```

### [Process Manager 2](https://github.com/Unitech/pm2)

```sh
pm2 start Lavalink
```

```sh
pm2 stop Lavalink
```

```sh
pm2 delete Lavalink
```

```sh
pm2 log Lavalink
```

```sh
pm2 [list|ls|status]
```

## [Tunnel Broker](https://tunnelbroker.net/)

### Configure

```sh
sysctl -w net.ipv6.ip_nonlocal_bind = 1
```

```sh
echo 'net.ipv6.ip_nonlocal_bind = 1' >> /etc/sysctl.conf
```

### Debian/Ubuntu

```sh
/etc/network/interfaces
```

```yaml
auto he-ipv6
iface he-ipv6 inet6 v4tunnel
        address 0:0:0::2 # Routed /48
        netmask 64
        endpoint 0.0.0.0 # Server IPv4 Address
        local 0.0.0.0 # Client IPv4 Address
        ttl 255
        gateway 0:0:0::1 # Routed /48
```

### Netplan (Linux)

```sh
/etc/netplan/99-he-tunnel.yaml
```

```yaml
network:
  version: 2
  tunnels:
    he-ipv6:
      mode: sit
      remote: 0.0.0.0 # Server IPv4 Address
      local: 0.0.0.0 # Client IPv4 Address
      addresses:
        - '0:0:0::/48' # Routed /48
      gateway6: '0:0:0::1' # Routed /48
```

### Route

```sh
ip -6 route replace local 0:0:0::/48 dev lo
```

### Test

```sh
ping6 google.com
```

```sh
ip -6 route
```

### Strategy

```yml
lavalink:
  server:
    # ...
    ratelimit:
      ipBlocks: ['0:0:0::/48'] # Routed /48
      strategy: "LoadBalance" # RotateOnBan | LoadBalance | NanoSwitch | RotatingNanoSwitch
```
