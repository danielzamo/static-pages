---
title: "Upgrade y/o downgrade versión IOS"
weight: 2
description: >
  Esta página resume una forma de realizar upgrade o downgrade de la versión de IOS.
---

### Procedimiento 1

```bash
Switch>en
Switch#dir

Switch#copy usbflash0:<NOMBRE_IMAGEN.EXTENSION> flash:
Switch#dir flash:
Switch#request platform software package install switch 1 file flash:<NOMBRE_IMAGEN.EXTENSION> auto-copy #<-- Al finalizar muestra el estado de la instalación.
Switch#reload                    #<-- [pide confirmación, 2 veces]
Switch>en
Switch#show ver
Switch#request platform software package clean #<-- Con esta se libera espacio de la flash. [pide confirmación, indicando que se va a borrar]




request platform software package clean     #<-- [pide confirmación]
```

### Procedimiento 2

```bash
Switch>en
Switch#dir
Switch#dir usbflash0:
Switch#copy usbflash0: bootflash:                                            #<-- [pide confirmación]
Switch#install add file bootflash:<NOMBRE_IMAGEN.EXTENSION> activate commit  #<-- [pide confirmación]
Switch>en
Switch#sh ver
Switch#wr
```


### Otros comandos

```bash
# Listar ficheros en flash
Switch#dir flash:

# Verificar imagen
Switch#verify /md5 bootflash:[NOMBRE_IMAGEN.EXTENSION]

# 
Switch#show run | i boot

# 
```

{{< tip >}}
Although, option 3 is great for quick testing, it is somewhat problematic when you want to update your theme. You would need to be careful not to overwrite your changes.
{{< /tip >}}

Once set, jump over to the [config.toml](https://github.com/onweru/compose/blob/afdf1cd76408aeac11547a6abd51bdc5138a295f/exampleSite/config.toml#L4-L7) file and start configuring your site.

