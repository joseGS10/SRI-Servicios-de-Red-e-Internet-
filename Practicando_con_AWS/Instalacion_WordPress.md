# PRÁCTICA AWS 
# Instalación de WordPress en instancia Linux(EC2) con soporte de base de datos RDS y almacenamiento EFS. 
El objetivo de esta práctica es montar un sitio web con WordPress, una base de datos (RDS) donde WordPress guardará la información, un disco compartido en red (EFS) donde WordPress guardará imágenes y archivos y todo ello dentro de una red privada de AWS (VPC). 

## PASO_1. Creación de la VPC 


Lo primero de todo es crear la red donde vivirá todo. Lo que haremos será crear una VPC con 2 subredes públicas y 2 subredes privadas y para ello, utilizaremos el asistente automático de AWS.  

Entramos en nuestra consola de AWS, en la barra de búsqueda escribimos VPC y en el listado de servicios que aparecen, sobre el servicio VPC, seleccionamos ‘Sus VPC’.  
El objetivo de esta práctica es montar un sitio web con WordPress, una base de datos (RDS) donde WordPress guardará la información, un disco compartido en red (EFS) donde WordPress guardará imágenes y archivos y todo ello dentro de una red privada de AWS (VPC).

<img width="940" height="296" alt="image" src="https://github.com/user-attachments/assets/b26e5f25-fe86-4881-b954-777069d14699" /> 
En la ventana que se abre donde deberían aparecer las VPC que tenemos creadas, pulsamos el botón naranja ‘Crear VPC’ 


<img width="940" height="239" alt="image" src="https://github.com/user-attachments/assets/56043e1e-b646-486c-9777-f69563f48d19" /> 


Pasamos a la ventana de configuración de la VPC en la que al tratarse de la creación de la VPC a partir de asistente seleccionamos **‘VPC y más’**. Le daremos un nombre a nuestra VPC: **‘wordpress’** y el resto de opciones las dejamos tal cual vienen ya definidas. Seguidamente pulsamos el botón **‘Crear VPC’** situado abajo del todo de la página. 




<img width="940" height="396" alt="image" src="https://github.com/user-attachments/assets/c8b82bff-78b6-407d-a115-f2a84bf7f94a" /> 


<img width="940" height="359" alt="image" src="https://github.com/user-attachments/assets/e120e166-f8aa-45a0-9519-95bf6275ba1e" /> 

Esperamos unos segundos a que se cree la VPC y pulsamos el botón naranja **‘Ver VPC’** y ya podemos visualizar las características de la VPC creada. 

<img width="940" height="556" alt="image" src="https://github.com/user-attachments/assets/de0b3728-0310-4fde-90eb-18caf23eba8e" />  


 
## PASO_2. Creación de instancias EC2.(Servidores) 

Vamos a lanzar una instancia con Debian en la subred pública 1. La llamaremos **‘servidorwordpress’** . Para ello, nos vamos al buscador de servicios, y buscamos EC2 y entramos. Pulsamos sobre el botón naranja **‘Lanzar instancia’** . 


<img width="940" height="409" alt="image" src="https://github.com/user-attachments/assets/45ea29b6-7370-4cba-b5b6-5e82b921a562" /> 
Ahora, tenemos que configurar la máquina virtual(instancia) que vamos a crear y le otorgaremos las siguientes características marcadas en los recuadros: 

<img width="940" height="766" alt="image" src="https://github.com/user-attachments/assets/749e4893-9f2d-4aeb-bcd3-78a3d9801008" /> 

Dentro del campo ‘Configuración de red’ pulsamos en **‘Editar’** y rellenamos los campos tal cual aparecen en la siguiente imagen: 

<img width="940" height="763" alt="image" src="https://github.com/user-attachments/assets/d2ebc9d5-4d1e-4387-912f-b31839ce393c" />  

Finalmente, pulsamos en **‘Lanzar instancia’** para terminar de crear la instancia. 

Y podemos ver el mensaje de que la instancia se lanzó (creó) correctamente. 

<img width="940" height="138" alt="image" src="https://github.com/user-attachments/assets/d4dea35e-10f1-4b1a-85be-637610c7dc45" /> 

<img width="940" height="212" alt="image" src="https://github.com/user-attachments/assets/5e9839e3-bbdb-4397-b2d3-56f54fa2c1f5" /> 

Ahora, necesitamos conectarnos con la instancia vía SSH.  


Para ello necesito la clave **vockey**. Esta nos la descargamos de :  


<img width="940" height="355" alt="image" src="https://github.com/user-attachments/assets/2973cc63-859d-4a3d-9e04-ea87d5a464cd" /> 
Y se nos descarga en nuestra carpeta Descargas de nuestro PC. Ahora abrimos el terminal de nuestro PC, nos desplazamos a la carpeta donde está el archivo **vockey** y desde ella, lanzamos el siguiente comando **ssh -i “labsuser.pem” admin@Ip_pública_de_la_instancia** 



<img width="940" height="345" alt="image" src="https://github.com/user-attachments/assets/48db8261-1d70-4146-b0e7-4179cf69e95f" /> 
Me está diciendo que es la primera vez que queremos conectar con este servidor y no lo conozco. ¿Te fías? Le decimos que sí ya que es el servidor que acabamos de crear. Respondemos con **yes**.  


Pero me da una advertencia y se refiere a que SSH se niega a utilizar la llave **‘labsuser.pem’** si además del propietario pueden usarla otros. Por tanto, tenemos que cambiar los permisos sobre este archivo y ponerlo que solo nosotros podemos usarlo. 

Y por fin, después de aplicar los permisos sobre el fichero claves para mi usuario solo, conseguimos entrar en la instancia. 

<img width="940" height="101" alt="image" src="https://github.com/user-attachments/assets/946a715c-5ef1-4095-a2fc-e27a596f7dd8" />  


 
## PASO_3. Instalación de Apache y PHP 

**Instalación de Apache** 

Ejecutamos los siguientes comandos(Para Apache): 

**sudo apt update** 

**sudo apt install apache2** 

**sudo systemctl start apache2** 

**sudo systemctl enable apache2** 

Para comprobar que el servidor Apache está bien instalado y funcionando ejecutamos en nuestro navegador **http://direccion_ip_pública_instacia** 



<img width="940" height="1115" alt="image" src="https://github.com/user-attachments/assets/6544f820-3b2b-434b-a3e3-0593d1758742" /> 

**Instalación de PHP** 

El intérprete PHP permite que el código fuente de WordPress se ejecute correctamente. 

Para la instalación de PHP ejecutamos los siguientes comandos: 

**sudo apt -y update && sudo apt upgrade** 

**sudo apt install php libapache2-mod-php php-cli -y** 

**sudo apt install mariadb-server php-mysql -y** (instalación de MySql también necesaria como módulo de Apache). 

**sudo systemctl restart apache2** (reiniciamos Apache) 

Y comprobamos que PHP está instalado con **php -v** 


<img width="940" height="215" alt="image" src="https://github.com/user-attachments/assets/84f89186-6911-42ca-b73f-758bb2cf1be5" />  


 
## PASO_4. Creación de la base de datos (RDS) 

Para ello, nos vamos al buscador del panel AWS y buscamos RDS para crear una base de datos. 

Una vez dentro de la ventana de creación de base de datos (RDS) pulsamos sobre **‘Crear una base de datos’**. La configuración que le hemos dado aparece en las siguientes ventanas marcadas con rectángulos rojos. El resto de opciones marcadas venían por defecto y se han dejado tal cual. 


<img width="940" height="662" alt="image" src="https://github.com/user-attachments/assets/5a0e7025-1041-4945-93d2-d3a1f52268bc" /> 


<img width="940" height="596" alt="image" src="https://github.com/user-attachments/assets/bf186a14-1b08-42c2-961a-fbb6c8614e1e" /> 

<img width="940" height="623" alt="image" src="https://github.com/user-attachments/assets/4130f75a-92b9-4ecd-b14b-d674ae486c85" /> 

<img width="940" height="546" alt="image" src="https://github.com/user-attachments/assets/aa474c79-7a5c-47cd-a032-f844ca2cb91e" /> 

<img width="940" height="520" alt="image" src="https://github.com/user-attachments/assets/1d7a5981-257c-479e-9303-fedc2239e856" /> 


<img width="940" height="331" alt="image" src="https://github.com/user-attachments/assets/00357f0e-a373-45bd-8cae-5e0230ad6eff" /> 

A continuación, le damos al botón **‘Crear base de datos’** 

Pasados unos minutos ya tenemos creada la base de datos. 


<img width="940" height="377" alt="image" src="https://github.com/user-attachments/assets/80465303-2be5-4f15-aa43-a731f89ae3c9" /> 

Una vez creada la base de datos deberemos hacer los ajustes necesarios en los grupos  de seguridad tanto de la instancia como de la base de datos para que la instancia pueda conectarse a la misma. Para ello, seleccionamos el **servicio VPC**, en el panel izquierdo que aparece, seleccionamos **Seguridad / Grupos de seguridad**. Nos lleva a una pantalla donde aparecen todos los grupos de seguridad existentes. Seleccionamos el grupo de seguridad que creamos para la base de datos **‘grupo-seguridad-bd’** . Y a continuación, damos a Editar reglas de entrada. Aquí debe aparecer el puerto 3306 y que todos puedan acceder 0.0.0.0/0 y le damos a **‘Guardar reglas’**. Con esto le estamos diciendo al firewall de la base de datos que permita la entrada por el puerto 3306 a todo el mundo(0.0.0.0/0) 

<img width="940" height="626" alt="image" src="https://github.com/user-attachments/assets/da26b4c9-1c2d-4e09-955e-62aa25478354" /> 

Por último, una vez creada la base de datos, es necesario establecer la conexión con la instancia. Para ello, abrimos el **cmd** de nuestra máquina y ejecutamos  

**ssh -i  labsuser.pem  admin@ 3.82.120.10** 


<img width="940" height="207" alt="image" src="https://github.com/user-attachments/assets/3c726fa2-d099-4934-afd7-bde566ed56c4" /> 

Estamos dentro de nuestra EC2. Debemos ejecutar: 

**sudo apt update** 

**sudo apt install default-mysql-client -y** 

**mysql -h dns-bbdd -u admin -p** 

<img width="940" height="83" alt="image" src="https://github.com/user-attachments/assets/19e500c8-3cbe-4157-a8b9-75ad6a224b0a" /> 
Me suelta este mensaje de error. Nuestro cliente de base de datos está intentado conectar de forma segura(cifrada) pero no reconoce el certificado seguridad de Amazon. 

Pasamos a descargarnos el certificado oficial de Amazon con el siguiente comando: 

**wget https://truststore.pki.rds.amazonaws.com/global/global-bundle.pem** 


<img width="941" height="246" alt="image" src="https://github.com/user-attachments/assets/3444c49b-4d50-4e08-8d54-ebc520c162ec" /> 

Volvemos a intentar conectarnos, pero esta vez con el certificado ya bajado. 

**mysql -h bdwordpress.cs5csmppcz34.us-east-1.rds.amazonaws.com -u admin -p --ssl-ca=global-bundle.pem** 


<img width="941" height="239" alt="image" src="https://github.com/user-attachments/assets/8811e789-b09a-46da-bfdd-b0e1b9846e25" />  


 
## PASO_5. Creación de Elastic File System(EFS) 

EFS es un sistema de almacenamiento externo(como un disco duro compartido) que conectaremos a la instancia y que más tarde conectaremos a wordpress. Este disco colgará de la carpeta **/var/www/html** del servidor Debian. WordPress vivirá en este disco externo resistente a fallos. 

Para comenzar con la creación de nuestro EFS nos vamos a la barra de búsqueda de AWS y escribimos EFS y entramos en dicho servicio picando sobre él.  

Seguidamente, pulsamos en el botón **‘Crear un sistema de archivos’** 

<img width="940" height="288" alt="image" src="https://github.com/user-attachments/assets/042b947d-337c-44bc-beca-d816bbc86d4b" /> 

La configuración de dicho sistema de almacenamiento aparece remarcada en la siguiente imagen: 


<img width="940" height="958" alt="image" src="https://github.com/user-attachments/assets/e089e063-e94a-4afe-976f-fcd8ba57bdfe" /> 
Y terminamos pulsando en **‘Crear un sistema de archivos’**. Podemos verlo creado en la siguiente imagen. 

<img width="940" height="310" alt="image" src="https://github.com/user-attachments/assets/09ac097f-5544-47a4-888a-804813c500b6" /> 
Al igual que la base de datos, el EFS tiene un firewall que por defecto bloquea cualquier intento de acceso. Hay que permitir que la instancia del servidor pueda entrar por lo que en el grupo de seguridad habrá que especificar una regla de entrada a tal efecto. 

Para ello, clicamos en el nombre de la EFS recién creada **’almacenamiento-wordpress’** , nos aparece una ventana con las diferentes configuraciones de la EFS. Picamos en la pestaña **Red** y vemos que nos aparecen 2 grupos de seguridad, pero en realidad es uno (ya que AWS lo replica por seguridad en 2 zonas distintas).Pinchamos en el enlace azul (ID de VPC) de cualquiera de ellos. Ahora en el panel lateral izquierdo vos vamos a **Seguridad / Grupos de Seguridad**. Nos aparecen un listado con todos los grupos de seguridad creados. Marcamos la casilla del ID equivalente al enlace azul que picamos anteriormente. Ahora en la parte de debajo de la pantalla picamos en ‘Reglas de entrada’. 


<img width="940" height="779" alt="image" src="https://github.com/user-attachments/assets/9419dd22-784a-4d6f-9afe-ac75fc5c9d06" /> 

Pulsamos el botón **‘Editar reglas de entrada’**. A continuación, pulsamos **‘Agregar regla’**  y la nueva línea que aparece la rellenamos tal cual aparece en la imagen: 

<img width="940" height="330" alt="image" src="https://github.com/user-attachments/assets/a56ee08e-6d92-4446-8273-3315163c1abe" /> 
Y pulsamos en **‘Guardar reglas’**. 


<img width="940" height="381" alt="image" src="https://github.com/user-attachments/assets/ce513f6f-ccc8-491d-b339-a32866bca855" /> 
Ahora, vamos a intentar montar el disco en la instancia EC2. 

Para ello, nos vamos de vuelta al cmd donde tenemos nuestra instancia EC2 abierta y ejecutamos **sudo mount -t nfs4 -o nfsvers=4.1,rsize=1048576,wsize=1048576,hard,timeo=600,retrans=2,noresvport fs-TU_ID_AQUI.efs.us-east-1.amazonaws.com:/ /var/www/html** 

<img width="941" height="150" alt="image" src="https://github.com/user-attachments/assets/cd10bb4a-a174-4a51-bddc-5687870fe82a" /> 

Me da error al montar. Vamos a intentarlo con la IP del EFS. 

También da error. Volvemos atrás para revisar grupos de seguridad. 

Hago un cambio en el comando colocando antes de la IP ,**addr=**10.0.138.91 


<img width="940" height="330" alt="image" src="https://github.com/user-attachments/assets/df161eed-7719-4f8d-93ab-6a20481c4787" /> 

Por fin, hemos conectado un disco duro de tamaño infinito (8.0E) a nuestro servidor. 

Se solucionó añadiendo la opción explícita **addr=10.0.138.91**. Esto fuerza la dirección de destino, evitando que el sistema tenga que deducirla o resolverla, y garantizando que el Kernel reciba los parámetros de conexión correctos para montar el volumen. 



## PASO_6. Descarga e Instalación de WordPress. 

Vamos a descargar los archivos de instalación de wordpress en **/tmp** 

Nos situamos en el directorio **/tmp** 


<img width="377" height="53" alt="image" src="https://github.com/user-attachments/assets/4c684bb9-22d6-43cb-ab3f-ce38846bf8a1" /> 
y descargamas wordpress con **wget https://wordpress.org/latest.tar.gz** 

descomprimimos el archivo con **tar -xzf latest.tar.gz** 

pasamos todo lo descomprimido a **/var/www/html/** con ***sudo cp -r /tmp/wordpress/* /var/www/html/*** 

eliminamos el index.html de prueba de apache con **sudo rm -f /var/www/html/index.html** 

damos permisos sobre la carpeta con **sudo chown -R www-data:www-data /var/www/html** 

ajustamos permisos de lectura y escritura con **sudo chmod -R 755 /var/www/html** 


**Creación de base de datos, usuario y contraseña** 

Ejecutamos este comando para entrar en mysql **mysql -u admin -h nombre-dns-rds-wordpress -p** 

Me da fallo y me dice que necesita un certificado 


<img width="940" height="62" alt="image" src="https://github.com/user-attachments/assets/4b3954ce-9bbb-4b32-827a-720756bfe9c4" /> 
Volvemos a descargar el certificado 

<img width="940" height="205" alt="image" src="https://github.com/user-attachments/assets/2a8a8bdb-58f2-4891-88f0-b09ed7f86849" /> 

Intentamos entrar de nuevo en la base de datos 


<img width="940" height="207" alt="image" src="https://github.com/user-attachments/assets/0226e5ec-7d4f-48ef-aeb5-2720fecb84e0" /> 

Ahora, ejecutamos los siguientes comandos: 

**CREATE DATABASE wordpress;** 

**CREATE USER 'wordpress_user'@'%' IDENTIFIED BY 'password123';** 

***GRANT ALL PRIVILEGES ON wordpress.* TO 'wordpress_user'@'%';*** 

**FLUSH PRIVILEGES;** 


<img width="940" height="381" alt="image" src="https://github.com/user-attachments/assets/2c62bb7d-ca56-4e4c-be5f-9344de3778f2" /> 
Finalmente, nos vamos a nuestro navegador y escribimos  http://IP_de_la_EC2. Ahora, si aparece la página de instalación de WordPress. 

Le damos los datos que nos solicitan y seguimos el asistente hasta que se completa la instalación de wordpress. 



<img width="940" height="871" alt="image" src="https://github.com/user-attachments/assets/4a400509-da2b-4b83-ab6c-2c86d85bc0da" /> 


<img width="940" height="356" alt="image" src="https://github.com/user-attachments/assets/a528c040-af18-4fbf-abc0-8b48d3e8d13b" /> 

<img width="940" height="1008" alt="image" src="https://github.com/user-attachments/assets/05dd2ef9-c546-46f5-894a-093c5c28b1ad" /> 


<img width="940" height="413" alt="image" src="https://github.com/user-attachments/assets/b3f2cc51-36b9-4300-b448-76dae187230f" />  


 

## PASO_7. Conexión de EFS a directorio WP_content. 

Vamos a ejecutar los siguientes comandos: 

Entramos en la carpeta de la web con  **cd /var/www/html** 

Renombramos la carpeta actual (Copia de seguridad) con **sudo mv wp-content wp-content-old** 

Creamos la carpeta nueva y vacía (será el punto de montaje) con  **sudo mkdir wp-content** 

Ahora, es cuando vamos a montar la unidad EFS en la carpeta recién creada **wp-content**. 

Para ello, ejecutamos  

**sudo mount -t nfs4 -o nfsvers=4.1,rsize=1048576,wsize=1048576,hard,timeo=600,retrans=2,noresvport fs-0ae8d4c6a3fe13fbc.efs.us-east-1.amazonaws.com:/ efs** 

donde cambiamos la última palabra **efs** por la ruta donde se encuentra el directorio donde montaremos este servicio  **/var/www/html/wp-content** 

Me suelta el siguiente error: 


<img width="940" height="116" alt="image" src="https://github.com/user-attachments/assets/6c22c7fc-9f03-415a-955c-25656329dfa8" /> 

Este mensaje de error es debido a que nuestro Debian requiere la instalación de una herramienta para entender los discos NFS. Tenemos que ejecutar los siguientes comandos: 

**sudo apt update** 

**sudo apt install nfs-common -y** 

Y ya podemos volver a intentar montar nuestro EFS en la carpeta **wp-content**. 

<img width="940" height="82" alt="image" src="https://github.com/user-attachments/assets/bf07c1e3-16e8-44ec-a8d2-edc0ec257b2e" /> 

Y por fin, queda montado. 

Ahora mismo, el disco EFS está vacío. Vamos a pasar el contenido de la carpeta **wp-content-old** dentro del nuevo disco montado y para ello ejecutamos el comando: 

***sudo cp -r /var/www/html/wp-content-old/* /var/www/html/wp-content/*** 

Y finalmente, proporcionamos permisos al usuario de Apapache sobre dicha carpeta donde se almacenará el contenido y así evitar errores en las subidas de datos futuros. 

**sudo chown -R www-data:www-data /var/www/html/wp-content** 

Y si volvemos a entrar en nuestro navegador y colocar la IP pública podemos ver que accedemos a la página de bienvenida de AWS. 


<img width="940" height="740" alt="image" src="https://github.com/user-attachments/assets/278a335a-f7f9-459c-be40-b88ef40b3241" /> 


**Si en un futuro queremos desmontar esta infraestructura recién creada deberíamos seguir los siguientes pasos:** 

1.	Desmontamos el EFS  con  **sudo umount /var/www/html/wp-content**
2.	Terminar la instancia ECS (la elimina) : Para ello, es estado de la instancia seleccionamos **‘Terminar’**
3.	Borramos la EFS (almacenamiento).
4.	Borramos la RDS (base de datos).
  
	




















