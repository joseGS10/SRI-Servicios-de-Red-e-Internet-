# Práctica 2º trimestre. Servidor de alojamiento Web.

Se pide la instalación, configuración y puesta en marcha de un servidor que ofrezca servicio de alojamiento web configurable: 

● Se dará alojamiento a páginas web tanto estáticas como dinámicas con “php”  

● Los clientes dispondrán de un directorio de usuario con una página web por defecto.  

● Además contarán con una base de datos sql que podrán administrar con phpmyadmin  

● Los clientes podrán acceder mediante ftp para la administración de archivos configurando adecuadamente TLS  

● Se habilitará el acceso mediante ssh y sftp. 

● Se automatizará mediante el uso de scripts: 

 ○ La creación de usuarios y del directorio correspondiente para el alojamiento web  
 
 ○ Host virtual en apache  
 
 ○ Creación de usuario del sistema para acceso a ftp, ssh, smtp, …  
 
 ○ Se creará un subdominio en el servidor DNS con las resolución directa e inversa  
 
 ○ Se creará una base de datos además de un usuario con todos los permisos sobre dicha base de datos (ALL PRIVILEGES)  
 
 ○ Se habilitará la ejecución de aplicaciones Python con el servidor web 
 

 Adicionalmente se podrá incluir: 
 
 ● Creación mediante Docker de un contenedor DNS y al menos un contenedor que actuará como servidor (web, mysql, ssh,...) Se configurará la red, volúmenes y scripts necesarios para ponerlos en marcha. Este apartado se valorará con hasta el 10% de la nota de la práctica.  
 

## Paso 0. **¿Qué es lo que tenemos que hacer?** 

Vamos a crear un servidor completo de hosting web, que incluye: 

Servidor web → Apache 

Lenguaje dinámico → PHP 

Base de datos → MySQL/MariaDB 

Administración web → phpMyAdmin 

Transferencia de archivos → FTP (con TLS) 

Acceso remoto → SSH + SFTP 

DNS propio → Bind9 

Automatización → Scripts Bash 

(Opcional) → Docker 

## Paso 1. **Configuración de red.** 


La MV donde vamos a llevar a cabo la práctica es **SRI-2-JB** en el nodo **marisma 000**. Dicha máquina dispone de la **interfaz virtual vmbr 9998** con salida a internet. 

La IP que recibe esta máquina por DHCP es **192.168.191.35/24** y debemos configurarla como estática por el simple hecho de que un servidor necesita una IP fija tanto para el DNS, como para Apache (virtual hosts), como para los clientes. 

Como se trata de una red interna vamos a presuponer que los únicos clientes que podrán hacer uso del servidor serán usuarios de la misma red interna. 

La puerta de enlace o **gateway es 192.168.191.101** 

<img width="512" height="43" alt="image" src="https://github.com/user-attachments/assets/3531f21b-912b-4694-ad97-3052fa74b872" />  

Configuración de red estática.  

<img width="941" height="421" alt="image" src="https://github.com/user-attachments/assets/1a0f5000-4d5e-4795-af0c-ac3d005ad5c0" />  

Guardamos y salimos. 

Aplicamos dicha configuración. 
<img width="940" height="277" alt="image" src="https://github.com/user-attachments/assets/0f5d11c3-9e1e-4116-b176-d9384fa12c71" /> 
No hay por qué preocuparse por los warning. 
Hacemos las siguientes comprobaciones. 

<img width="940" height="410" alt="image" src="https://github.com/user-attachments/assets/21271971-1632-4271-a963-36951d5a62c4" /> 
<img width="940" height="260" alt="image" src="https://github.com/user-attachments/assets/84f4d317-ae52-4213-92fd-250679db710c" /> 
<img width="940" height="260" alt="image" src="https://github.com/user-attachments/assets/58daf7d4-bb18-46fe-8373-8c9a45777db1" /> 

## Paso 2. Instalación del Servidor Web (Apache + MySQL + PHP + Python)

Actualizamos los repositorios con **sudo apt update** y **sudo apt upgrade**. 

A continuación, vamos a instalar **Apache HTTP Server** que es un servicio que escucha las peticiones por el puerto 80(HTTP), sirve páginas web a sus clientes y gestiona hosts virtuales. 

**sudo apt install apache2 -y** 

Para verificar la correcta instalación y la activación del servicio ejecutamos **systemctl status apache2** 

<img width="940" height="342" alt="image" src="https://github.com/user-attachments/assets/c7f36bf0-52e5-4d34-984b-f88cdf01ba21" /> 

Comprobamos su funcionamiento abriendo el navegador y colocando la IP de nuestro servidor y debemos ver la página por defecto de Apache. 

<img width="940" height="350" alt="image" src="https://github.com/user-attachments/assets/d65e5a80-9daf-4170-b8e3-a1ca9d88f353" /> 

Hemos instalado el **servidor web Apache**, que permitirá ofrecer contenido web a través del protocolo HTTP. Hemos verificado que el **servicio está activo** y funciona correctamente accediendo a su página web por defecto desde el navegador. 


Seguimos con la "instalación de PHP". 

Para ello, ejecutamos **sudo apt install php libapache2-mod-php php-mysql -y** 


**php** instala el núcleo del lenguaje PHP 

**libapache2-mod-php** es el módulo que conecta Apache con PHP, permitiendo que el servidor intérprete código dinámico en lugar de mostrarlo como texto plano 

**php-mysql** módulo que permite la conexión PHP - MySQL 


La instalación de PHP nos va a permitir el manejo de páginas dinámicas, conexión con MySQL, aplicaciones web(phpMyAdmin). 



Vamos a ver si Apache y PHP se comunican correctamente. Para ello,vamos a crear un archivo con código PHP. El directorio principal donde Apache busca las páginas Web por defecto es /var/www/html/ 

Vamos a crear allí un archivo llamado **info.php** 
<img width="941" height="119" alt="image" src="https://github.com/user-attachments/assets/7ecdc368-f573-4664-8b51-1ddfec999361" /> 
Reiniciamos el servidor para que coja todos los cambios que ha sufrido con	**sudo systemctl restart apache2** 


Ahora, abrimos el navegador y comprobamos si Apache es capaz de mostrarnos el contenido de ese archivo **.php** que acabamos de crear. 













