

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


5. Verificamos que Docker está funcionando:  

**sudo systemctl status docker**  

<img width="576" height="214" alt="Screenshot_1" src="https://github.com/user-attachments/assets/f5b43d9b-f5b5-43e2-9c94-10968a4d50d4" />  

Si Docker no está ejecutándose, lo podemos iniciar manualmente con **sudo systemctl start docker**  

## Actividad 2. Primeros comandos.  



1 Ejecución del primer contenedor **sudo docker run hello-world**  

<img width="577" height="201" alt="Screenshot_1" src="https://github.com/user-attachments/assets/7c64a0b7-2f33-48ad-a7a7-3b873d448957" /> 

2 Nos descargamos la imagen ‘alpine’ (es un linux muy ligerito) del registro de Docker y la guardamos en nuestro sistema. 

	**docker image pull alpine** 
 






