---
title: "Ruteo estático/simétrico, en RHEL 8/7 (y/o derivados)"
date: 2021-09-15T22:48:07+02:00
draft: false
---

> En este documento se configura el ruteo estático (y simétrico?) sobre un Linux basado en RHEL ver 8 (funcional en ver 7 y/o derivados). 
>
> Lo de _simétrico_, esta en version _beta_, en teoria deberia funcionar en modo simétrico, sin inconvenientes, pero en producción aun no se han validado las pruebas (al 15/09/2021).
>
> La configuracion es aplicable a sistemas que tengan dos placas interfaces ethernet. Y en principio no necesita de las ip rule. En esta [representación][caso.ruteo.estatico]

![Representación caso de ruteo estático y simétrico][caso.ruteo.estatico]

# Fichero /etc/sysconfig/network-scripts/ifcfg-enp1s0

```
cat /etc/sysconfig/network-scripts/ifcfg-enp1s0
TYPE=Ethernet
PROXY_METHOD=none
BROWSER_ONLY=no
BOOTPROTO=none
DEFROUTE=yes
IPV4_FAILURE_FATAL=no
IPV6INIT=yes
IPV6_AUTOCONF=yes
IPV6_DEFROUTE=yes
IPV6_FAILURE_FATAL=no
NAME=enp1s0
UUID=e4b027b9-68bf-4d07-b747-388dc23cd368
DEVICE=enp1s0
ONBOOT=yes
IPADDR=192.168.122.30
PREFIX=24
GATEWAY=192.168.122.1
DNS1=192.168.122.30
DOMAIN=my-asus.net
IPV6_PRIVACY=no
DNS2=192.168.122.1
```

# Fichero /etc/sysconfig/network-scripts/route-enp1s0

```
cat /etc/sysconfig/network-scripts/route-enp1s0
ADDRESS0=192.168.122.0
NETMASK0=255.255.255.0
GATEWAY0=192.168.122.1
```

# Fichero /etc/sysconfig/network-scripts/ifcfg-enp8s0

```
TYPE=Ethernet
BOOTPROTO=none
IPV4_FAILURE_FATAL=no
IPV6INIT=no
NAME=enp8s0
DEVICE=enp8s0
ONBOOT=yes
IPADDR=10.0.0.30
PREFIX=24
IPV6_PRIVACY=no
```

# Fichero /etc/sysconfig/network-scripts/route-enp8s0

```
cat /etc/sysconfig/network-scripts/route-enp8s0
ADDRESS0=10.0.0.0
NETMASK0=255.255.255.0
GATEWAY0=10.0.0.1
```

[caso.ruteo.estatico]: /images/ruteo.simetrico.estatico.png "Representación caso de ruteo estático y simétrico"
