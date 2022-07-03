+++
title = "Comando dig"
description = "Comando dig"
date = "2021-09-14"
author = "Daniel Oscar Zamo"
draft="True"
+++

# Utilidad `dig`

## Busqueda desde un server-root
La siguiente secuencia de comandos, es por simular una busqueda recursiva de un autoritativo de zona. En particular un registro de tipo **A** `www.axians.com`. Es basicamente una busqueda completa que realiza un cache DNS, si ha dicho registro, no lo tiene almacenado previamente.
Lo interesante a observar aqui, es:

- Un primer y simple uso de la herramienta `dig`.
- Observar la información que muestra el comando. En particular los flag, segun el servidor que haya respondido.

_Consultando los server-root_

> `dig` (a secas), muestra cuales son los server-root. A continuacion se invoca `dig` y que su respuesta sea la simplificada.

```
# dig +short
dig +short
e.root-servers.net.
k.root-servers.net.
d.root-servers.net.
b.root-servers.net.
g.root-servers.net.
l.root-servers.net.
a.root-servers.net.
c.root-servers.net.
i.root-servers.net.
h.root-servers.net.
j.root-servers.net.
f.root-servers.net.
m.root-servers.net.
```

**¿Consultar sobre el server-root `e.root-servers.net.` por el dominio `www.axians.com`**

```
dig @e.root-servers.net. www.google.com
```
> Los _**server-root**_ no responden con **IP** de servidores (registros **DNS** del **tipo A**) del dominio completo consultado (`www.axians.com`, en este ejemplo), pues su finalidad no es almacenar este tipo de numeros **IP**.
> 
> Sus respuestas son indicando que servidores del tipo **TLD** (servidores **Top Level Domain**) gestionan el **dominio de nivel superior** consultado. En este caso `com.`

**¿Consultar sobre el TDL `a.gtld-servers.net.` el dominio `www.axians.com`?**

```
dig @a.gtld-servers.net. www.axians.com
```

> Los TLD tampoco tienen la responsabilidad de responder con registros del tipo A o autoritativos, pues no registran numeros de IP. Si que responden en que servidor autorativo de la zona del dominio.

**Consultar sobre el servidor autoritativo `ns2.perf1.fr.` de la zona, el registro (de tipo **A**) de `www.axians.com`**

```
dig @ns2.perf1.fr. www.axians.com

; <<>> DiG 9.16.1-Ubuntu <<>> @ns2.perf1.fr. www.axians.com
; (2 servers found)
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 7792
;; flags: qr aa rd; QUERY: 1, ANSWER: 2, AUTHORITY: 0, ADDITIONAL: 1
;; WARNING: recursion requested but not available

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 1232
; COOKIE: 6c610dbf6918de4f0100000061424dcf6277d8a66a434e03 (good)
;; QUESTION SECTION:
;www.axians.com.                        IN      A

;; ANSWER SECTION:
www.axians.com.         86400   IN      CNAME   axians.com.
axians.com.             300     IN      A       20.71.232.110

;; Query time: 32 msec
;; SERVER: 81.92.87.76#53(81.92.87.76)
;; WHEN: mié sep 15 21:47:27 CEST 2021
;; MSG SIZE  rcvd: 101
```

# Referencias

- https://root-servers.org/ <— servidores root
- https://eltallerdelbit.com/resolucion-dns-dig-linux/
- https://blog.standalonecomplex.es/2010/05/16/guia-de-dig/
- http://somebooks.es/comando-dig-ubuntu/
- https://rm-rf.es/como-usar-el-comando-dig-ejemplos/
- https://www.hostinger.es/tutoriales/comando-dig-linux
- https://www.administracionderedes.com/consulta-iterativa-recursiva/
- https://www.ibm.com/docs/en/zos/2.2.0?topic=uzudc-dig-command-query-name-servers
- https://learn.akamai.com/en-us/webhelp/pulsar-diagnostic-tools/pulsar-diagnostic-tools/GUID-80B4B9A1-16B2-4D70-8100-7B2617102002.html
- https://www.youtube.com/watch?v=sUhEqT_HSBI&ab_channel=ProfeSang <— Video explicado simple y bien
- https://www.cloudns.net/blog/10-most-used-dig-commands/ <— Ejemplo de como consultar un TLD (top level domain)
- https://www.cloudns.net/wiki/article/40/
- https://es.wikipedia.org/wiki/Sistema_de_nombres_de_dominio <— tipos de registros DNS
- https://stackoverflow.com/questions/706077/dns-how-to-dig-thru-the-right-domain-name-from-the-tld
- https://www.youtube.com/watch?v=LqSqrtxrW7w&ab_channel=DefectoDigital <— buena explicacion de DNS
- https://www.youtube.com/watch?v=PLTndeCSdzk&ab_channel=UniversitatPolit%C3%A8cnicadeVal%C3%A8ncia-UPV
