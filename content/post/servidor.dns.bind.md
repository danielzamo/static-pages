+++
title = "Servidor de DNS con Bind"
description = "Instalacion y configuracion de servidor DNS con Bind"
date = "2021-09-14"
author = "Daniel Oscar Zamo"
+++

> Este documento incluye la configuracion de:
>
> El despliegue incluye dos redes (una es LAN interna y la otra una WAN
> Se configuran las resoluciones directas e inversas
> Se definen dos vistas.

Las tareas realizadas son hechas sobre un servidor con una instalación de distribución CentOS Stream 8.

La representación de la arq. de red del despliegue se muestra en [esta captura][server.dns.bind].

![Servidor DNS Bind][server.dns.bind]

[server.dns.bind]: /images/server.dns.bind.png "Arq. inicial - serv. DNS bind, c/2 interface sobre redes diferentes"

_Especificación:_

- Red internal: `10.0.0.0/24`
- IP internal: `10.0.0.30`
- Red external: `192.168.122.0/24`
- IP external: `192.168.122.30`

# Instalación y configuración red interna

## Instalar Bind

```
dnf -y install bind bind-utils
```

## Fichero /etc/named.conf

```
cat /etc/named.conf

acl internal-network { 10.0.0.0/24; };
acl external-network { 192.168.122.0/24; };
options {
        listen-on port 53 { any; };
        listen-on-v6 { any; };
        directory       "/var/named";
        dump-file       "/var/named/data/cache_dump.db";
        statistics-file "/var/named/data/named_stats.txt";
        memstatistics-file "/var/named/data/named_mem_stats.txt";
        secroots-file   "/var/named/data/named.secroots";
        recursing-file  "/var/named/data/named.recursing";
        allow-query     { localhost; internal-network; external-network; };
        allow-transfer  { localhost; };
        recursion yes;
        dnssec-enable yes;
        dnssec-validation yes;
        managed-keys-directory "/var/named/dynamic";
        pid-file "/run/named/named.pid";
        session-keyfile "/run/named/session.key";
        include "/etc/crypto-policies/back-ends/bind.config";
};
logging {
        channel default_debug {
                file "data/named.run";
                severity dynamic;
        };
};
view "internal" {
        match-clients { localhost; internal-network; };
        zone "." IN { type hint; file "named.ca"; };
        zone "my-asus.net" IN { type master; file "my-asus.net.lan"; allow-update { none; }; };
        zone "0.0.10.in-addr.arpa" IN { type master; file "0.0.10.db"; allow-update { none; }; };
        include "/etc/named.rfc1912.zones";
        include "/etc/named.root.key";
};
view "external" {
        match-clients { any; };
        allow-query { any; };
        recursion no;
        zone "my-asus.net" IN { type master; file "my-asus.net.wan"; allow-update { none; }; };
        zone "30.122.168.192.in-addr.arpa" IN { type master; file "30.122.168.192.db"; allow-update { none; }; };
};
```

## Solo resolver en IPV4

Conf. resolucion solo en IPV4

```
# Habilitar resolucion de DNS solo para IPV4
echo 'OPTIONS="-4"'  >> /etc/sysconfig/named
```

## Configurar archivos de zona interna

### Resolucion directa

```
cat /var/named/centos-stream-main.my-asus.net.lan

$TTL 86400
@   IN  SOA    centos-stream-main.my-asus.net.    root.my-asus.net. (
        2021110901  ;Serial
        3600        ;Refresh
        1800        ;Retry
        604800      ;Expire
        86400       ;Minimum TTL
)
        IN  NS      centos-stream-main.my-asus.net.
        IN  A       10.0.0.30
centos-stream-main     IN  A    10.0.0.30
www                    IN  A    10.0.0.31
```

### Resolucion inversa

```
cat /var/named/0.0.10.db

$TTL 86400
@   IN  SOA     centos-stream-main.my-asus.net. root.my-asus.net. (
        2021110901  ;Serial
        3600        ;Refresh
        1800        ;Retry
        604800      ;Expire
        86400       ;Minimum TTL
)
        IN  NS      centos-stream-main.my-asus.net.
30      IN  PTR     centos-stream-main.my-asus.net.
31      IN  PTR     www.my-asus.net.
```

## Configurar archivos de zona external

### Resolucion directa

```
cat /var/named/30.122.168.192.db

$TTL 86400
@   IN  SOA     centos-stream-main.my-asus.net. root.my-asus.net. (
        2021110903  ;Serial
        3600        ;Refresh
        1800        ;Retry
        604800      ;Expire
        86400       ;Minimum TTL
)
        IN  NS      centos-stream-main.my-asus.net.
30      IN  PTR     centos-stream-main.my-asus.net.
31      IN  PTR     www.my-asus.net.
```

### Resolucion inversa

```
cat /var/named/my-asus.net.wan

$TTL 86400
@   IN  SOA    centos-stream-main.my-asus.net.    root.my-asus.net. (
        2021110903  ;Serial
        3600        ;Refresh
        1800        ;Retry
        604800      ;Expire
        86400       ;Minimum TTL
)
        IN  NS      centos-stream-main.my-asus.net.
        IN  A       192.168.122.30
centos-stream-main     IN  A    192.168.122.30
www                    IN  A    192.168.122.31

ns1                    IN  CNAME    centos-stream-main.my-asus.net.
```

## Habilitar y verificar resolucion

### Habilitar servicio DNS

#### Habilitar e iniciar Bind

```
systemctl enable --now named
```

#### Habilitar en firewall

Habilitar el servicio DNS si firewalld esta activado

```
firewall-cmd --add-service=dns --permanent
firewall-cmd --reload
```

## Modificar el resolver, apuntando al aqui configurado (Si es necesario)

En esta caso la especion es:

- _Ip DNS_: 192.168.122.30/24
- _Interface_: enp1s0

```
nmcli connection modify enp1s0 ipv4.dns 192.168.122.30
nmcli connection down enp1s0; nmcli connection up enp1s0
```

### Probando resolucion

```
dig centos-stream-main.my-asus.net
dig -x 10.0.0.30
```
