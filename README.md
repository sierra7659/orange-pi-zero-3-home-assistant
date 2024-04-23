# Instalación de Home Assistant en Orange Pi Zero 3 🛠️

**Índice**
1. [Preparativos](#id1)
2. [Configuración de red](#id2)
3. [Instalación](#id3)
4. [Configuración de acceso externo a traves de DuckDNS](#id4)


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

3 - **IMPORTANTE** cambiar nombre de nuestro wifi 2.4GHz, por nuestro nombre, y contraseña, debe ir entre comillas "
```sh
sudo nmcli dev wifi connect "NOMBRE DE NUESTRO WIFI" password "CONTRASEÑA"
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

Desde aqui recomiendo usar SSH para poder ir copiando los comandos.
Para acceder por ssh, abrimos un Terminal/Powershell:
_cambiar 192.168.0.33 por la ip de Orange Pi Zero 3_
```sh
ssh orangepi@192.168.0.33
```
Nos pedirá contraseña (Contraseña `orangepi`) y si queremos crear un fingerprint, damos enter. 

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

# Configuración de acceso externo a traves de DuckDNS 🐥 <a name="id4"></a>
Una vez tengamos instalado y configurado los parametros mas básicos de **Home Assistant**.

Procedemos a lo siguiente:

1 - Ir a nuestro **Usuario** > Activamos **Modo Avanzado**

2 - Ahora vamos a **Ajustes** > **Complementos** > **Tienda de Complementos**

3 - Buscamos **File Editor**, clicamos e instalamos.

Una vez instalado debe aparecer algo asi:
<img width="1175" alt="image" src="https://github.com/sierra7659/orange-pi-zero-3-home-assistant/assets/50959412/322a08eb-83e1-4327-95c6-03fd9ef86978">
_Debemos activar **Vigilancia** y **Mostrar en Panel Lateral**_

4 - Volvemos a la **Tienda de complementos**

5 - Buscamos **DuckDNS**

6 - Instalamos y NO iniciamos

7 - Vamos a https://www.duckdns.org/

8 - Nos registramos
<img width="922" alt="image" src="https://github.com/sierra7659/orange-pi-zero-3-home-assistant/assets/50959412/49244ee7-087d-4da6-a162-036e0fd1c76a">

9 - Nos aparecerá esta pantalla
<img width="1326" alt="DuckDNS" src="https://github.com/sierra7659/orange-pi-zero-3-home-assistant/assets/50959412/017110cc-048a-4b9e-884b-852b0d068f44">

10 - Debemos introducir el dominio que deseamos, para acceder a nuestro **Home Assistant** desde el exterior. Por ejemplo *haorangepi* **IMPORTANTE** Poner nombre distinto.

11 - Damos a create y nos ha generado en Domains el dominio, con nuestra IP Pública.

12 - Volvemos a **Home Assitant** , clicamos en el Complemento de **DuckDNS**, vamos a la pestaña **Configuración**
<img width="1060" alt="Captura de pantalla 2024-04-23 a las 13 12 01" src="https://github.com/sierra7659/orange-pi-zero-3-home-assistant/assets/50959412/ed17d9e5-0c84-4a37-89db-89aa07ffc95c">

13 - Añadimos en Domains el nombre que hemos agregado en el paso 10. Por ejemplo: 
__haorangepi.duckdns.org__

14 - En **Token** tenemos que agregar el Token que hay en **DuckDNS** **IMPORTANTE** NO COMPARTIR ESTE TOKEN CON NADIE

15 - En el apartado de **Let's Encrypt** 
debemos poner 
```
accept_terms: true
```

16 - Damos a **Guardar**

17 - Vamos a **Herramientas de Desarrollador**, damos a Reiniciar. Y posteriormente **Reiniciar el Sistema**
<img width="494" alt="image" src="https://github.com/sierra7659/orange-pi-zero-3-home-assistant/assets/50959412/f1694531-1fcb-40a9-bada-f5d8afdab100">

18 - Esperamos a que reincie. Vamos a **File Editor**, damos al **Icono de carpeta**

19 - Buscamos **configuration.yaml**


<img width="314" alt="File Editor" src="https://github.com/sierra7659/orange-pi-zero-3-home-assistant/assets/50959412/55fae7ec-f82b-4b7d-85cb-e51aff237a2d">

20 - Al comienzo del archivo pegamos esto:
```yaml
# DuckDNS
http:
  ssl_certificate: /ssl/fullchain.pem
  ssl_key: /ssl/privkey.pem
```

21 - Guardamos dando al icono del Disquete
<img width="61" alt="image" src="https://github.com/sierra7659/orange-pi-zero-3-home-assistant/assets/50959412/154f6dd4-507d-485b-a2e3-0e8cc14d9a3c">

22 - Vamos a **Herramientas de Desarrollador** y clicamos en **Verificar Configuración**

23 - Si aparece bien, deberemos **reinciar de nuevo**.

24 - Despues de esto para acceder ahora tendremos que acceder por https, es decir en vez de http.
Ej. 

```http
https://192.168.0.33:8123
```

25 - Una vez accedamos, iremos a **Ajustes** > **Sistema** > **Red** > **URL de Home Assistant** > En Internet escribimos nuestro dominio de DuckDNS (Ej. https://haorangepi.duckdns.org) 

26 - Deberemos redireccionar los puertos, desde el exterior 443 a 8123

# Créditos
- https://www.youtube.com/watch?v=-ujrSWD4W0o&ab_channel=JohnDo (Video Soporte del proceso)
- https://www.home-assistant.io/installation/linux (Instalación Home Assistant en Linux)
- https://github.com/home-assistant/supervised-installer?tab=readme-ov-file (Github de Supervised installer)
- https://docs.docker.com/engine/install/debian/ (Instalar Docker en Debian)


