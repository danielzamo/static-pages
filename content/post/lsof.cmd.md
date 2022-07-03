---
title: "El comando lsof"
date: 2021-09-15T22:34:53+02:00
draft: false
---

# Listar todos los archivos abiertos

Para listar todos los archivos abiertos, ejecutar `lsof` sin argumentos

> _Warn:_ La salida puede tardar y/o ser muy extensa. Recordemos que en un sistema basado en Uni**X** (como lo es Linux) todos los elementos son ficheros.

```
lsof
```
# Listar archivos abiertos por procesos pertenecientes a un usuario en particular

```
lsof -u <USER-NAME>
```

# Referencias

- [Linux lsof command][lsof.command]

[lsof.command]: https://www.howtoforge.com/linux-lsof-command/ "Comando Linux lsof"
