

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

<img width="576" height="107" alt="Screenshot_1" src="https://github.com/user-attachments/assets/d2ba882b-5d9d-4ea3-9cd5-fb2a0ae9c27f" /> 

4. Ejecutamos un contenedor basado en la imagen ‘alpine’ 

**docker run alpine ls -l** 

‘alpine’ es una imagen de un linux ligero que cuando se ejecute en el contenedor sacará un listado de su directorio actual y finaliza la ejecución del contenedor automáticamente. 

<img width="576" height="337" alt="Screenshot_1" src="https://github.com/user-attachments/assets/119c99d1-61ca-4a6b-9738-9303e6088a93" /> 

5. En esta ocasión, creamos un contenedor con la imagen de ‘alpine’, y dicho contenedor de linux  ejecuta el comando echo y se cierra. 

**docker run alpine echo “hello from alpine”** 
 
 <img width="576" height="63" alt="Screenshot_1" src="https://github.com/user-attachments/assets/8c0ecbef-9ef9-41b5-9066-49668e2f64d1" /> 

6. Volvamos a ejecutar una nueva instancia del contenedor Alpine, se ejecuta el comando **/bin/sh** que simplemente abre la consola y a continuación finaliza dicho contenedor(realmente no vemos nada, pero el contenedor se ejecutó y terminó). 
 
**docker run alpine /bin/sh** 

<img width="574" height="61" alt="Screenshot_1" src="https://github.com/user-attachments/assets/b2595379-8f3d-4c7d-9b6b-3b30b47e34f5" /> 

7. Si lo que realmente queremos es interactuar con el intérprete de comando, en la ejecución de dicho contenedor tenemos que especificar la opción **- it** y así si podemos ejecutar comandos en dicho intérprete. (**- it** nos permite interactuar en el contenedor que ejecutamos) 


**docker run -it alpine /bin/sh**

<img width="568" height="338" alt="Screenshot_1" src="https://github.com/user-attachments/assets/3c9a02db-1e10-4f12-980e-92b69fb9b762" /> 

Si queremos salir de la consola y finalizar la ejecución de dicho contenedor basta con escribir **exit** e **Intro**. 

8. Si queremos visualizar el listado de todos los contenedores que se están ejecutando actualmente usamos el comando **docker container ls** 

<img width="575" height="61" alt="Screenshot_1" src="https://github.com/user-attachments/assets/069925c4-74b4-4999-bcbc-7ac709f0073d" /> 

Como podemos ver no se hace referencia a ningún contenedor porque no hay ninguno en ejecución. 

9. Si queremos sacar una lista de los contenedores que hemos ejecutado hasta el momento usamos la orden **docker container ls -a** 

<img width="576" height="206" alt="Screenshot_1" src="https://github.com/user-attachments/assets/f01716d9-5961-47d9-bd44-8021626d805b" /> 

10. Cada contenedor ejecutado aunque se trate de la misma imagen actúa de forma independiente, es decir, dichos contenedores no guardan relación entre ellos, no pueden interactuar entre ellos.

Así si ejecutamos un contenedor de la siguiente forma **docker run -it alpine /bin/ash**,  ejecutamos dentro de él el comando **echo “Hello world” > hello.txt** y listamos su contenido con **ls** y después finalizamos la ejecución de dicho contenedor, podemos ver que este contenedor no guarda relación alguna si ejecutamos otro contenedor de la misma imagen listando su contenido con **docker run alpine ls** 

<img width="555" height="125" alt="Screenshot_1" src="https://github.com/user-attachments/assets/96b62675-4e44-42b2-9245-e50267c4453b" /> 

En la ejecución de este segundo contenedor podemos ver que no existe el archivo **hello.txt** creado en el anterior contenedor (no existe relación entre ellos) 

<img width="503" height="282" alt="Screenshot_1" src="https://github.com/user-attachments/assets/f69762d7-511c-4618-a655-335661352301" /> 

Los contenedores se ejecutan en instancias diferentes e independientes entre sí. 


11. Para poder volver a ejecutar una misma instancia de contenedor que ejecutamos con anterioridad debemos conocer el **ID** que se le atribuyó a dicho contenedor. Para ello, listamos el conjunto de contenedores que hemos ejecutado hasta el momento con **docker container ls -a** y posteriormente ejecutamos el comando **docker container start -i <ID>**
(dicho ID basta con escribir sus primeros 3 o 4 caracteres identificativos).

<img width="575" height="213" alt="Screenshot_1" src="https://github.com/user-attachments/assets/f6f4727e-0e7c-4231-8307-6dcfde05d872" /> 

<img width="506" height="65" alt="Screenshot_1" src="https://github.com/user-attachments/assets/9cf05fae-c8d9-4eca-bdcf-cbb5da535e45" /> 

12. Con **docker container ls** listamos los contenedores que tenemos activos y
con **docker container exec <ID> ls** lista el contenido del contenedor de ID indicado.

<img width="575" height="263" alt="Screenshot_1" src="https://github.com/user-attachments/assets/bd6bc499-c054-49da-98ac-3902ad81f04a" /> 

## Actividad 3. Almacenamiento y redes en docker.

Los contenedores son efímeros, es decir, la configuración que le proporcionamos y todos los datos y ficheros que tengamos en él prevalecen después de parar dicho contenedor. El único motivo que puede destruir su contenido es destruyendo dicho contenedor. 


Los datos persisten en los contenedores y para ello, Docker nos proporciona varias soluciones. Entre ellas nombramos: los **volúmenes docker** y los **bind mount**. 


**Volúmenes Docker** -> los datos se guardarán en /var/lib/docker/volumes. Este tipo de volúmenes se usan en los siguientes casos: 

 para compartir datos entre volúmenes. 
 
 para realizar copias de seguridad 
 
 para cuando desee almacenar los datos de mi contenedor en un proveedor cloud en vez de en local. 
 


**Bind mounts** -> con ello conseguimos montar el sistema de ficheros del contenedor en una parte de mi sistema de ficheros del sistema operativo. Ello nos permitirá: 

 Compartir ficheros entre el host y el contenedor. 
 
 Que otras aplicaciones que no sean docker tengan acceso a esos ficheros. 
 


**Uso de volúmenes docker** 

13. Primero creamos un volumen docker con **docker volume create miweb**

<img width="575" height="63" alt="Screenshot_1" src="https://github.com/user-attachments/assets/9ac40c37-8e05-472f-bd3b-8739b0d3798f" /> 

14. A continuación, creamos (ejecutamos) un contenedor con el volumen asociado (usando **-v**) y creamos un fichero index.html

<img width="575" height="290" alt="Screenshot_1" src="https://github.com/user-attachments/assets/e7e308cd-3543-4f68-b3cb-819977a4b11b" /> 

15. Ahora que hemos eliminado el contenedor anterior, creamos uno nuevo con el mismo volumen utilizado en el anterior y vamos a ver que no hemos perdido la información del fichero index.html

<img width="575" height="95" alt="Screenshot_1" src="https://github.com/user-attachments/assets/8cd65c5c-7552-4963-beec-d55bf1e6eb54" /> 

**Uso de bind mount** 

16. Creamos un directorio en el sistema del host, donde crearemos un fichero index.html. Montamos ese directorio en un contenedor y comprobamos que realmente estamos sirviendo el fichero que tenemos en el directorio que hemos creado.

<img width="576" height="186" alt="Screenshot_1" src="https://github.com/user-attachments/assets/d46647d0-9345-4961-b5eb-f8180ca1e68d" /> 

17. Eliminamos dicho contenedor y volvemos a crear otro con el directorio montado y el resultado sigue siendo el esperado. De hecho, podemos modificar el contenido del fichero montado en el contenedor.

<img width="575" height="60" alt="Screenshot_1" src="https://github.com/user-attachments/assets/4a905187-8b27-47c9-8276-9e6a3f204108" /> 

**Ejemplo 1. Despliegue de la aplicación Guestbook** 

Vamos a desplegar una aplicación web que requiere de dos servicios (web y base de datos). Dicha aplicación es servida por el puerto 5000/tcp. Utilizaremos la imagen iesgn/guestbook. Esta aplicación guarda la información en una base de datos no relacional, que utiliza el puerto 6379/tcp para conectarnos. Usaremos la imagen redis. 


Para que los contenedores puedan encontrarse por su nombre (como si fuera una red local), primero debemos crear la red.  

<img width="574" height="42" alt="Screenshot_1" src="https://github.com/user-attachments/assets/089c086a-0a1d-449c-bf00-e7daa3e35128" /> 

Ahora levantamos el motor de base de datos. Usamos -v /opt/redis:/data para decirle a Docker: "Guarda los datos en la carpeta física de mi ordenador, no solo dentro del contenedor". Así, si el contenedor se borra, los datos sobreviven. 

<img width="576" height="220" alt="Screenshot_1" src="https://github.com/user-attachments/assets/01b5fb58-6025-4bc4-b602-9430d2d985e1" /> 

Levantamos la aplicación hecha en Python. Al usar --network red_guestbook, la app buscará automáticamente a un equipo llamado redis en esa red. El parámetro -p 80:5000 conecta el puerto 80 de tu ordenador al 5000 del contenedor.  

<img width="576" height="233" alt="Screenshot_1" src="https://github.com/user-attachments/assets/5ac34b0d-1a53-4651-9b8a-c3cba3fc8114" /> 

Abrimos el navegador web y entramos en la dirección http://localhost. Deberíamos ver la aplicación Guestbook cargada. Escribimos un mensaje y lo guárdamos para comprobar que la conexión a Redis funciona.  

<img width="576" height="198" alt="Screenshot_1" src="https://github.com/user-attachments/assets/41b7846b-fea3-41b6-a895-64db6ea6cb8f" /> 

¿Qué pasaría si, en lugar de llamar al contenedor redis, lo llamamos por ejemplo, contenedor_redis).? 

Por defecto, el código fuente de esta aplicación está programado para buscar la palabra redis. Si le cambiamos el nombre al contenedor de la base de datos, la aplicación web fallará porque no lo encuentra. Para solucionarlo, Docker te permite "inyectar" una variable de entorno (-e REDIS_SERVER=contenedor_redis) en el momento de crear el contenedor web. Esto sobrescribe la configuración por defecto y le dice a la app el nuevo nombre que debe buscar. 

**Ejemplo 2. Despliegue de la aplicación temperatura.**


Este ejercicio es muy interesante porque introduce el concepto de los **microservicios** y las **aplicaciones sin estado** (stateless). Esto significa que, a diferencia del ejercicio anterior con Redis, aquí si borras los contenedores no pierdes información crítica, ya que la aplicación simplemente consulta y muestra datos, no los almacena permanentemente. 

Pasos: 
1. **Creamos la red de comunicación**: Es vital para que el frontend y el backend se encuentren por nombre (DNS interno de Docker).

<img width="576" height="39" alt="Screenshot_1" src="https://github.com/user-attachments/assets/6d8b3742-7c32-4fd3-aefd-de663e7e3d03" /> 


2. **Desplegamos el microservicio Backend (La API):** Este contenedor procesa las consultas de temperaturas. No usamos -p, por lo que queda oculto al navegador y solo accesible desde dentro de la red de Docker.

<img width="575" height="205" alt="Screenshot_1" src="https://github.com/user-attachments/assets/3e0602a1-7aab-46e6-b0bf-8cdb2bb40f9f" /> 

3. **Desplegamos el microservicio Frontend (La web)**: Esta es la interfaz gráfica. Publicamos el puerto 3000 interno del contenedor hacia el puerto 80 de nuestro ordenador.  

<img width="576" height="62" alt="Screenshot_1" src="https://github.com/user-attachments/assets/d153f89b-e75c-41a4-9c25-2d8c53caea21" /> 

4. Vamos al navegador y abrimos la aplicación.

<img width="577" height="242" alt="Screenshot_1" src="https://github.com/user-attachments/assets/7b539f17-48a2-464b-b17f-11e040eba857" /> 


Ahora vamos a replicar la situación del cambio de nombre para entender cómo conectar servicios cuando no usan los nombres por defecto.  


1. Borramos los contenedores actuales para liberar el puerto 80 de tu máquina física.

<img width="577" height="57" alt="Screenshot_1" src="https://github.com/user-attachments/assets/f3989527-59bd-42d1-bbc6-c87f93558513" /> 

2. **Desplegamos el Backend con un nombre personalizado**: Esta vez lo bautizamos como temperaturas-api.

<img width="577" height="57" alt="Screenshot_1" src="https://github.com/user-attachments/assets/83bed714-030e-409b-a24d-041f0347b07b" /> 

3. **Desplegamos el Frontend sobreescribiendo la configuración:** Usamos -e TEMP_SERVER=temperaturas-api:5000 para indicarle a la web exactamente dónde y en qué puerto debe buscar los datos ahora.

<img width="575" height="55" alt="Screenshot_1" src="https://github.com/user-attachments/assets/5a609c40-968f-4538-a9ef-398d01187a45" /> 

4. Accedemos al navegador y verificamos.

<img width="577" height="243" alt="Screenshot_1" src="https://github.com/user-attachments/assets/db76647c-dd7b-42e7-b6c8-13d51b7efb7d" /> 

**Ejemplo 3. Despliegue de Wordpress + Mariadb** 


1. Creamos la red de WordPress

<img width="576" height="77" alt="Screenshot_1" src="https://github.com/user-attachments/assets/64ccc37f-0dc1-4359-a37f-e7b24b848dd7" /> 

2. Desplegamos el motor de Base de Datos (mariaDB).Podemos observar en la cantidad de variables de entorno (-e) que usamos aquí. Le estamos diciendo a MariaDB cómo se llama la base de datos, el usuario y las contraseñas antes de que arranque, para que lo configure todo automáticamente. Además, guardamos los datos en tu disco duro en /opt/mysql_wp.

<img width="576" height="235" alt="Screenshot_1" src="https://github.com/user-attachments/assets/63c214a4-979d-40fe-ab89-b29eeab721aa" /> 

3. Desplegamos el Servidor Web (WordPress).Ahora levantamos WordPress. Lo conectamos a la misma red y usamos las variables WORDPRESS_DB_... para pasarle exactamente los mismos datos de conexión que configuramos en el paso anterior. Gracias a esto, WordPress no nos pedirá configurar la base de datos a mano.

<img width="576" height="315" alt="Screenshot_1" src="https://github.com/user-attachments/assets/6b33f236-52ff-4dc5-8ab3-17c5136e20b1" /> 

4. Comprobamos que ambos contenedores están corriendo.

<img width="575" height="97" alt="Screenshot_1" src="https://github.com/user-attachments/assets/eb4e2799-8ab6-4848-b34c-65695a254457" /> 

5. Comprobamos que podemos acceder a Wordpress desde el navegador.

<img width="577" height="330" alt="Screenshot_1" src="https://github.com/user-attachments/assets/14785280-4144-414c-9cd5-cac4897d3f87" /> 


































































 








 






