# Instalación de Rocky Linux 8

## Descargar Rocky Linux
Descargar desde [sitio web](https://rockylinux.org/download), version 8.10, Minimal

## Crear VM

 - Nombre: el que gusten
 - CPU: 1 CPU 
 - RAM: 1GB 
 - HDD: 15GB
 - Interfaz de Red: Modo NAT

### Instalación

 - Idioma: INGLISH!!!!!
 - Network & Hostname: Interfaz en ON
 - Time & Date: America/Montevideo
 - Installation Destination: Dejar `Storage Configuration` en `Automatic`
 - Asignar Password de Root
 
 Boton `Begin Installation`, y listo. Cuando termina, click en `Reboot System`

## Post-Instalación

#### Conectarse por SSH
Iniciamos sesión en el servidor (con el password de root especificado), y verificamos IP de la VM, con el comando `ip a`.

Es el valor de `inet`, en la interfaz que NO ES `lo` (localhost)

Con esa IP, nos conectamos por SSH a la VM con PuTTY, o en el cmd de Windows/Linux/loquesea usar el comando ssh:
`ssh root@IP`

### Desactivar Firewall de Red (firewalld)
Ejecutamos:
```bash
# Detenemos firewall
systemctl stop firewalld

# Desactiva el inicio automatico del firewall
systemctl disable firewalld
```
### Desactivar Firewall de aplicaciones (SELinux)
Ejecutamos `setenforce 0` para apagar SELinux

Editar el archivo con  `/etc/selinux/config` usando `vi` y reemplazar `SELINUX=enforcing` por `SELINUX=disabled`

O usar `sed` para buscar `enforcing` y reemplazarlo por `disabled` dentro del archivo:

`sed -i 's/enforcing/disabled/g' /etc/selinux/config`

 ### Instalar repositorio EPEL
 Ejecutar `dnf install epel-release -y`

### Cambiar hostname del sistema
Ejecutar `hostnamectl set-hostname NOMBRE`

### Instalar paquetitos utiles
Instalar con dnf:
`dnf install PAQUETE -y`	

- open-vm-tools (herramienta para aplicar Paravirtualización - FUNDAMENTAL)
 - vim (como vi, pero mejorado - con colorcitos)
 - git (el propio)
 - wget (descarga archivos de internet)
 - net-tools (herramientas para ver estado de conexiones)
 - unzip 
 - unrar

### Agregar almacenamiento (opcional, depende del caso)

Primero, agregar otro disco duro a la VM, y reiniciar la VM

Verificar con `lsblk` que el disco este en el sistema, como `sda` o `sdb`, con el tamaño que indicaron

Para particionar, ejecutar `fdisk /dev/sda`.

Dentro de fdisk, apretamos `n` para nueva particion, dejamos valores por defecto (ajustan el tamaño maximo de particion para el disco automaticamente), y guardamos cambios con `w`

Esto genera un archivo `/dev/sda1` que representa la particion.

Para formatear la particion, ejecutamos `mkfs.ext4 /dev/sda1`

Si es necesario, crear punto de montaje, por ejemplo `mkdir /mnt/disco`

Y montar el dispositivo con `mount /dev/sda1 /mnt/disco`

Para hacer el montaje persistente, agregar al final de `/etc/fstab` la siguiente linea:

`/dev/sda1		/mnt/disco		ext4	defaults 	0 0`

Para terminar, aceptar los cambios de `/etc/fstab` con el comando `systemctl daemon-reload`
