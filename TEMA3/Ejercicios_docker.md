
# EMPEZANDO CON DOCKER

## Actividad 1. Instalación de Docker en una MV Linux Ubuntu

Vamos a hacer una instalación binaria de Docker Engine. (aunque también hay versiones de escritorio). 

En nuestro caso usaremos los repositorios apt de Docker, para ello:  

1.Actualizamos los repositorios e instalamos los paquetes necesarios para instalar  desde repositorios HTTPS: 

 **sudo apt-get update** 
 
 **sudo apt-get install ca-certificates curl** 
 

2.Añadir la clave GPG oficial de Docker:  


 **sudo install -m 0755 -d /etc/apt/keyrings** 
 
 **curl -fsSL https://download.docker.com/linux/debian/gpg | sudo gpg --dearmor -o** 
 **/etc/apt/keyrings/docker.gpg** 
 
 **sudo chmod a+r /etc/apt/keyrings/docker.gpg**
 

3.Añadimos el repositorio oficial de Docker: 


**sudo tee /etc/apt/sources.list.d/docker.sources <<EOF** 

**Types: deb** 

**URIs: https://download.docker.com/linux/ubuntu** 

**Suites: $(. /etc/os-release && echo** 
**"${UBUNTU_CODENAME:-$VERSION_CODENAME}")** 

**Components: stable** 

**Architectures: $(dpkg --print-architecture)** 

**Signed-By: /etc/apt/keyrings/docker.asc** 

**EOF** 




4.Instalamos Docker Engine: 

**sudo apt update** 

**sudo apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin** 
**docker-compose-plugin**  


