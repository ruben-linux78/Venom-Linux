Instalación de Qemu & Virt Manager en Venom Linux (SysV init).

$ sudo scratch install qemu virt-manager libvirt libvirt-glib libvirt-python openbsd-netcat dnsmasq iptables nftables

$ modprobe kvm-<your CPU>  (For AMD use kvm-amd & for Intel use kvm-intel )

$ sudo usermod -aG kvm,libvirt <username>

Configuración:

$ sudo vim /etc/libvirt/libvirtd.conf

Comprobar y descomentar estas lineas:
1. listen_tls = 0
2. unix_sock_group = “libvirt”
3. unix_sock_ro_perms = “0777”
4. unix_sock_rw_perms = “0770”
5. unix_sock_dir = “/run/libvirt”
6. auto_unix_ro = “polkit” >>> auto_unix_ro = “none”
7. auto_unix_rw = “polkit” >>> auto-unix_rw = “none”
Guardar y salir:

$ sudo vim /etc/libvirt/libvirt.conf

Descomentar:
1. uri_default = “qemu:///system”
Guardar y salir:

$ sudo vim /etc/libvirt/qemu.conf

Ir a la linea 519 aprox. en USER:
Descomentar línea: 519 and 523 aprox.
1. user = “your username”
2. group = “libvirt”
 
Habilitar servicios añadiendolos en: /etc/rc.conf

$ sudo vim /etc/rc.conf

libvirtd virtlogd

$ sudo reboot

Si tras crear tu primera maquina virtual te da fallo de “unable to open /dev/net/tun”
Necesitas cargar el modulo del kernel de tun, para que tu maquina pueda comunicarse con el host anfitrion y conectarse a internet.

Para comprobar si el modulo de tun esta cargado:

$ lsmod | grep tun

Si el comando no muestra ningun resultado, el modulo no esta activo.

Para habilitarlo:

$ sudo modprobe tun

Para comprobarlo:

$ lsmod | grep tun

Para comprobar que nos ha creado el directorio de /dev/net/tun con permisos de escritura (rw):

$ ls -l /dev/net/tun

crw-rw-rw- 1 root root 10, 200 … /dev/net/tun

En caso de que el directorio no este creado:

$ sudo mkdir -p /dev/net

$ sudo mknod /dev/net/tun c 10 200

$ sudo chmod 666 /dev/net/tun

Para hacer que el modulo del kernel de tun se cargue al inicio del sistema dos opciones:

1. Creamos el archivo: /etc/modules-load.d/qemu-modules.conf y ponemos en el tun:

$ vim /etc/modules-load.d/qemu-modules.conf

tun

 2. Lo incluimos en /etc/rc.local.conf /sbin/modprobe tun ó modprobe tun, para que lo cargue tras iniciar el sistema:

$ vim /etc/rc.local

/sbin/modprobe tun

$ sudo reboot
