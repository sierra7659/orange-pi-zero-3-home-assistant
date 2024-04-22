# Instalación de Home Assistant en Orange Pi Zero 3 🛠️

**Índice**
1. [Preparativos](#id1)
2. [Configuración de red](#id2)
3. [Instalación](#id3)


## 🚧 Preparativos <a name="id1"></a>
Antes de nada necesitamos una imagen de un Sistema Operativo, en este caso necesitamos Debian 11 para nuestra **Orange Pi Zero 3 4GB**. 

Podemos descargarlo [aquí](https://drive.google.com/file/d/1p6k3GYTI_icz_yG44gLtTSEd2LaWKDdO/view?usp=drive_link) (_versión de 4GB_)

Una vez tengamos esto necesitamos montar la imagen.
Lo haremos con el siguiente programa [Etcher](https://github.com/balena-io/etcher/releases/download/v1.18.11/balenaEtcher-Portable-1.18.11.exe)

Con Etcher abierto, seleccionamos la imagen que hemos descargado anteriormente. 

Y posteriormente seleccionamos la SD donde queremos montarlo.

Tras acabar, conectamos la SD a nuestra **Orange Pi Zero 3**.


Encendemos la **Orange Pi Zero 3** y la conectaremos a la red.

## 🛜 Configuración de Red <a name="id2"></a>

Para poder conectar nuestra **Orange Pi Zero 3** tenemos que hacerlo por comandos. Con los siguientes:

1 - Encendemos el WIFI
```sh
nmcli radio wifi on
```
2 - Nos muestra WIFIs disponibles
```sh
nmcli dev wifi list
```
Nos aseguramos que este el nuestro.

3 - **IMPORTANTE** cambiar nombre de nuestro wifi 2.4GHz, por nuestro nombre, y contraseña, debe ir entre comillas " (Para escribir estas comillas **SHIFT + 2**)
```sh
sudo nmcli dev wifi connect NOMBRE_DE_NUESTRO_WIFI password "CONTRASEÑA"
```

4 - Asegurarnos que nos hemos conectado
```sh
ping google.com
```

Nos tiene que dar algo asi:
```sh
PING google.com (172.217.17.14): 56 data bytes
64 bytes from 172.217.17.14: icmp_seq=0 ttl=116 time=10.833 ms
64 bytes from 172.217.17.14: icmp_seq=1 ttl=116 time=16.791 ms
64 bytes from 172.217.17.14: icmp_seq=2 ttl=116 time=16.451 ms
```

Si no es asi, deberemos intentar conectarnos de nuevo al wifi.

## 🛠️ Instalación <a name="id3"></a>

Instalamos el __Supervised Installer__ como sudo:
```sh
sudo su
```
Introducimos la contraseña de sudo que es `orangepi`

Ahora si, instalamos las dependencias:
```sh
apt install \
apparmor \
cifs-utils \
curl \
dbus \
jq \
libglib2.0-bin \
lsb-release \
network-manager \
nfs-common \
systemd-journal-remote \
systemd-resolved \
udisks2 \
wget -y
```
ó (si no funciona el comando, usarlo en una linea)

```sh
apt install apparmor cifs-utils curl dbus jq libglib2.0-bin lsb-release network-manager nfs-common systemd-journal-remote systemd-resolved udisks2 wget -y
```


### Instalamos __Docker__ para __Debian__:
```sh
for pkg in docker.io docker-doc docker-compose podman-docker containerd runc; do sudo apt-get remove $pkg; done
```

Agregamos el repositorio `apt` de __Docker__
```sh
sudo apt-get update
```
```sh
sudo apt-get install ca-certificates curl
```
```sh
sudo install -m 0755 -d /etc/apt/keyrings
```
```sh
sudo curl -fsSL https://download.docker.com/linux/debian/gpg -o /etc/apt/keyrings/docker.asc
```
```sh
sudo chmod a+r /etc/apt/keyrings/docker.asc
```
```sh
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/debian \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```
```sh
sudo apt-get update
```

Instalamos la última versión de Docker
```sh
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

Verificamos que se haya instalado bien

```sh
exit
```
*Hacemos exit, para salir del sudo. Si no nos funciona debemos agregar un grupo de docker, como explica [aquí](https://docs.docker.com/engine/install/linux-postinstall/)*
```sh
docker run hello-world
```


### Descargamos OS Agent

```sh
wget https://github.com/home-assistant/os-agent/releases/download/1.6.0/os-agent_1.6.0_linux_aarch64.deb
```

Descomprimimos
```sh 
sudo dpkg -i os-agent_1.6.0_linux_aarch64.deb
```

Comprobamos si ha sido instalado correctamente
```sh
gdbus introspect --system --dest io.hass.os --object-path /io/hass/os
```

Deberia aparecer varias interfaces, no hay que realizar nada mas.


### Instalamos Home Assistant Supervised Debian Package
```sh
wget -O homeassistant-supervised.deb https://github.com/home-assistant/supervised-installer/releases/latest/download/homeassistant-supervised.deb
```
```sh
sudo apt install ./homeassistant-supervised.deb
```

⚠️📝 Saldrá un menu que debemos seleccionar con las flechas del teclado.
`Raspberrypi3-64`

Tras esto, puede que de un error del estilo de 
```sh
N: Download is performed unsandboxed as root file...
```

**IMPORTANTE**: 
Esperar unos minutos, podemos ir tirando este comando para ver si ya esta en ejecución **Home Assistant**.

```sh
docker ps
```
cuando este en ejecución nos saldrá, tiene que salirnos unas **6 filas**.

Ahi ya podremos acceder a nuestro **Home Assistant**

Vamos a nuestro navegador y accedemos a la IP del Orange Pi Zero 3 (ej. 192.168.0.33)
```http
http://192.168.0.33:8123
```

Esperamos a que acabe de instalar, y listo!


# Créditos
- https://www.youtube.com/watch?v=-ujrSWD4W0o&ab_channel=JohnDo (Video Soporte del proceso)
- https://www.home-assistant.io/installation/linux (Instalación Home Assistant en Linux)
- https://github.com/home-assistant/supervised-installer?tab=readme-ov-file (Github de Supervised installer)
- https://docs.docker.com/engine/install/debian/ (Instalar Docker en Debian)


