

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



1. Ejecución del primer contenedor **sudo docker run hello-world**  

<img width="577" height="201" alt="Screenshot_1" src="https://github.com/user-attachments/assets/7c64a0b7-2f33-48ad-a7a7-3b873d448957" /> 

2. Nos descargamos la imagen ‘alpine’ (es un linux muy ligerito) del registro de Docker y la guardamos en nuestro sistema. 

**docker image pull alpine**  

<img width="577" height="193" alt="Screenshot_1" src="https://github.com/user-attachments/assets/02d494c8-4693-4543-a5e6-6eb1b2e13ad3" />  

3. Para listar las imágenes Docker que tenemos en nuestro sistema. 

**docker image ls**  

<img width="576" height="157" alt="Screenshot_1" src="https://github.com/user-attachments/assets/d2ba882b-5d9d-4ea3-9cd5-fb2a0ae9c27f" /> 

4. Ejecutamos un contenedor basado en la imagen ‘alpine’ 

**docker run alpine ls -l** 

‘alpine’ es una imagen de un linux ligero que cuando se ejecute en el contenedor sacará un listado de su directorio actual y finaliza la ejecución del contenedor automáticamente. 

<img width="576" height="337" alt="Screenshot_1" src="https://github.com/user-attachments/assets/119c99d1-61ca-4a6b-9738-9303e6088a93" /> 

5. En esta ocasión, creamos un contenedor con la imagen de ‘alpine’, y dicho contenedor de linux  ejecuta el comando echo y se cierra. 

**docker run alpine echo “hello from alpine”** 
 
 <img width="576" height="83" alt="Screenshot_1" src="https://github.com/user-attachments/assets/8c0ecbef-9ef9-41b5-9066-49668e2f64d1" /> 

6. Volvamos a ejecutar una nueva instancia del contenedor Alpine, se ejecuta el comando **/bin/sh** que simplemente abre la consola y a continuación finaliza dicho contenedor(realmente no vemos nada, pero el contenedor se ejecutó y terminó). 
 
**docker run alpine /bin/sh** 

<img width="574" height="81" alt="Screenshot_1" src="https://github.com/user-attachments/assets/b2595379-8f3d-4c7d-9b6b-3b30b47e34f5" /> 

7. Si lo que realmente queremos es interactuar con el intérprete de comando, en la ejecución de dicho contenedor tenemos que especificar la opción **- it** y así si podemos ejecutar comandos en dicho intérprete. (**- it** nos permite interactuar en el contenedor que ejecutamos) 


**docker run -it alpine /bin/sh**

<img width="568" height="338" alt="Screenshot_1" src="https://github.com/user-attachments/assets/3c9a02db-1e10-4f12-980e-92b69fb9b762" /> 

Si queremos salir de la consola y finalizar la ejecución de dicho contenedor basta con escribir **exit** e **Intro**. 

8. Si queremos visualizar el listado de todos los contenedores que se están ejecutando actualmente usamos el comando **docker container ls** 

<img width="575" height="81" alt="Screenshot_1" src="https://github.com/user-attachments/assets/069925c4-74b4-4999-bcbc-7ac709f0073d" /> 

Como podemos ver no se hace referencia a ningún contenedor porque no hay ninguno en ejecución. 

9. Si queremos sacar una lista de los contenedores que hemos ejecutado hasta el momento usamos la orden **docker container ls -a** 

<img width="576" height="206" alt="Screenshot_1" src="https://github.com/user-attachments/assets/f01716d9-5961-47d9-bd44-8021626d805b" /> 

10. Cada contenedor ejecutado aunque se trate de la misma imagen actúa de forma independiente, es decir, dichos contenedores no guardan relación entre ellos, no pueden interactuar entre ellos.

Así si ejecutamos un contenedor de la siguiente forma **docker run -it alpine /bin/ash**,  ejecutamos dentro de él el comando **echo “Hello world” > hello.txt** y listamos su contenido con **ls** y después finalizamos la ejecución de dicho contenedor, podemos ver que este contenedor no guarda relación alguna si ejecutamos otro contenedor de la misma imagen listando su contenido con **docker run alpine ls** 

<img width="555" height="165" alt="Screenshot_1" src="https://github.com/user-attachments/assets/96b62675-4e44-42b2-9245-e50267c4453b" /> 

En la ejecución de este segundo contenedor podemos ver que no existe el archivo **hello.txt** creado en el anterior contenedor (no existe relación entre ellos) 

<img width="503" height="282" alt="Screenshot_1" src="https://github.com/user-attachments/assets/f69762d7-511c-4618-a655-335661352301" /> 

Los contenedores se ejecutan en instancias diferentes e independientes entre sí. 


11. Para poder volver a ejecutar una misma instancia de contenedor que ejecutamos con anterioridad debemos conocer el **ID** que se le atribuyó a dicho contenedor. Para ello, listamos el conjunto de contenedores que hemos ejecutado hasta el momento con **docker container ls -a** y posteriormente ejecutamos el comando **docker container start -i <ID>**
(dicho ID basta con escribir sus primeros 3 o 4 caracteres identificativos).

<img width="575" height="213" alt="Screenshot_1" src="https://github.com/user-attachments/assets/f6f4727e-0e7c-4231-8307-6dcfde05d872" /> 

<img width="506" height="85" alt="Screenshot_1" src="https://github.com/user-attachments/assets/9cf05fae-c8d9-4eca-bdcf-cbb5da535e45" /> 

12. Con **docker container ls** listamos los contenedores que tenemos activos y
con **docker container exec <ID> ls** lista el contenido del contenedor de ID indicado.

<img width="575" height="263" alt="Screenshot_1" src="https://github.com/user-attachments/assets/bd6bc499-c054-49da-98ac-3902ad81f04a" /> 





















 








 






