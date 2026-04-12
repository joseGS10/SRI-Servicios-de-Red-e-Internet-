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
<img width="940" height="340" alt="image" src="https://github.com/user-attachments/assets/647d3daa-8dba-4e8f-9d37-277393a1f05e" /> 
Y efectivamente, estamos viendo la ejecución del archivo info.php. Esta ejecución nos muestra un montón de información como la versión de PHP, módulos instalados y demás información que podría usar un atacante para encontrar vulnerabilidades por lo que una vez comprobado su funcionamiento se aconseja su borrado con **sudo rm /var/www/html/info.php** 


Continuamos con la **instalación** del intérprete del lenguaje **Python** y el módulo WSGI que es el estándar que permite a Apache comunicarse y ejecutar aplicaciones escritas en Python de forma eficiente. 


**sudo apt install python3 libapache2-mod-wsgi-py3** 


Para concluir con este bloque de instalaciones, vamos a **instalar un servidor de Bases de Datos y phpMyAdmin.** 

Toda página web dinámica de cierta complejidad (foro, tienda online o un WordPress) necesita de un lugar donde guardar la información(artículos, contraseñas, configuraciones..) y ese es el Servidor de Base de Datos.


Instalamos el servidor y cliente de la base de datos ejecutando 

**sudo apt install mariadb-server mariadb-client -y** 


Ahora, comprobamos que el motor de base de datos está arrancado  


**systemclt status mariadb** 
<img width="940" height="315" alt="image" src="https://github.com/user-attachments/assets/9e1b34a0-08cc-4fd6-a46a-64d3509639df" /> 
Cuando instalamos el servidor de base de datos, este crea un usuario sin contraseña (root) el cual utiliza el administrador para entrar por primera vez y configurar las cosas, además de otros usuarios anónimos sin nombre ni contraseña y que pueden ser usados por cualquiera para entrar como anónimo al sistema. Y esto supone un peligro si el servidor se expone a internet. Por lo que se aconseja , una vez instalado el servidor de Base de Datos, ejecutar el siguiente script de seguridad 

**sudo mysql_secure_installation** 

y debemos contestar a las preguntas con lo siguiente: 

1.	Intro
  
2.	n + Intro
   
3.	y + Intro + contraseña segura para root + Intro
   
4.	y + Intro
   
5.	y + Intro
   
6.	y+ Intro
   
7.	y + Intro
   

Finalizamos **instalando phpMyAdmin** para que yo y los usuarios del servicio de alojamiento podamos gestionar las bases de datos con un entorno gráfico muy amigable desde el navegador. Esta aplicación web, actúa como un cliente gráfico (frontend) que interactúa directamente con el motor MariaDB, permitiendo crear, modificar y eliminar bases de datos de forma intuitiva desde cualquier navegador web. 


**sudo apt install phpmyadmin -y** 


Comprobamos que funciona escribiendo en el navegador **http://192.168.191.35/phpmyadmin** 


<img width="940" height="423" alt="image" src="https://github.com/user-attachments/assets/6f9519f7-a6d2-4438-a3d8-128b399d790c" /> 


<img width="940" height="558" alt="image" src="https://github.com/user-attachments/assets/a343fb65-5f46-4862-8d1b-ccf4ad6754d1" /> 

## Paso 3. Creación manual de un entorno de clientes(Usuarios + Directorios Web + Virtual Hosts). 


En este paso, vamos a tratar de validar manualmente el funcionamiento del sistema antes de automatizarlo mediante scripts. 

Para comprender y validar la arquitectura de un entorno de Hosting Compartido multiusuario, he procedido a la creación manual de dos cuentas de clientes distintas (**cliente1** y **cliente2**). Esto nos va a permitir demostrar dos pilares fundamentales: **el aislamiento de seguridad a nivel de sistema operativo** (cada usuario solo va a tener permiso sobre su propio directorio web) y **el enrutamiento basado en nombres de Apache** (el servidor será capaz de servir sitios web diferentes a través de la misma dirección IP 192.168.191.35 leyendo la cabecera ‘Host’ de la petición HTTP del visitante). 

Este proceso implica la creación de usuarios a nivel de sistema (Linux), la asignación de directorios de trabajo (DocumentRoot) y la configuración de sus host virtuales (Virtual Host) en Apache para el enrutamiento correcto de su dominio. 



3.1 Creación de Directorios y Webs. 


Necesitamos dos carpetas independientes y dos páginas web distintas para que, al probarlo, sepamos con certeza qué página nos está devolviendo el servidor. 

Apache necesita una carpeta física por cliente donde éste guardará sus archivos. 


Creación de Carpetas (Directorios Web) 


**sudo mkdir /var/www/html/cliente1** 

**sudo mkdir /var/www/html/cliente2** 


Ahora mismo esas carpetas pertenecen al administrador (root). Ahora, vamos a asignarlas al usuario www-data de Apache para garantizar el correcto acceso del servidor web. Posteriormente, se modificará la propiedad para que cada cliente sea dueño de su propio espacio web, manteniendo al servidor Apache con permisos de lectura.Tenemos que hacer que estas carpetas sean propiedad absoluta de cada cliente en cuestión para que puedan subir cosas y para ello ejecutamos 


**sudo chown -R www-data:www-data /var/www/html/cliente1** 

**sudo chown -R www-data:www-data /var/www/html/cliente2** 


Además, daremos permisos totales sobre dichas carpetas a sus propietarios actuales, y de solo lectura y ejecución para el grupo y resto. 


**sudo chmod -R 755 /var/www/html/cliente1** 

**sudo chmod -R 755 /var/www/html/cliente2** 

Con esto, conseguimos que Apache pueda servir las páginas. 


Creamos la web del Cliente1: 


**sudo nano /var/www/html/cliente1/index.html** 

<img width="940" height="73" alt="image" src="https://github.com/user-attachments/assets/43acfc25-f67f-4aba-99fd-18b735ecdfdf" /> 

Guardamos (Ctrl+o, Enter) y salimos (Ctrl+x) 


Creamos la web del Cliente2: 


**sudo nano /var/www/html/cliente2/index.html** 

<img width="940" height="77" alt="image" src="https://github.com/user-attachments/assets/99c9b0c4-8697-422b-9042-ac068dd0f2a6" /> 
Guardamos (Ctrl+o, Enter) y salimos (Ctrl+x) 
3.2 Creación de los Virtual Host.
Un Virtual Host es un método de configuración en servidores web como Apache que permite alojar múltiples sitios web (con diferentes nombres de dominio) en una única máquina física y compartiendo una misma dirección IP. Cuando un navegador realiza una petición a la IP del servidor, incluye de forma invisible una cabecera HTTP llamada **Host** (p.ej www.cliente1.marisma.local) . Apache lee esta cabecera interna, la compara con las directivas **ServerName** de todos los archivos de configuración **.conf** activados, y enruta la petición hacia el **DocumentRoot** (la carpeta) correspondiente, devolviendo los archivos correctos al visitante de forma transparente. 


Virtual Host del cliente1 

 
**sudo nano /etc/apache2/sites-available/cliente1.conf** 
<img width="940" height="427" alt="image" src="https://github.com/user-attachments/assets/d7b4ad56-a1fd-48b9-a65c-bd004e8a6392" /> 

Guardamos (Ctrl+o, Enter) y salimos (Ctrl+x) 


Virtual Host del cliente2 

 
**sudo nano /etc/apache2/sites-available/cliente2.conf** 
<img width="940" height="435" alt="image" src="https://github.com/user-attachments/assets/09df884c-3b60-4c61-a068-80f28c962ad9" /> 

Guardamos (Ctrl+o, Enter) y salimos (Ctrl+x) 


Ahora, tenemos que activar ambos sitios y recargar Apache para que tome los cambios.. 


**sudo a2ensite cliente1.conf** 

**sudo a2ensite cliente2.conf** 

**sudo systemctl reload apache2** 



Dado que en esta fase de la implementación no hemos desplegado el servidor DNS, hemos modificado el archivo estático de resolución de nombres (**/etc/hosts**) del equipo cliente de pruebas. Esto nos va a permitir simular la resolución DNS y validar que el motor de Apache está respondiendo de forma correcta para los diferentes clientes. 


Editamos dicho archivo e incluimos un par de líneas. 

**sudo nano /etc/hosts** 
<img width="940" height="348" alt="image" src="https://github.com/user-attachments/assets/c75047cf-440e-486d-b913-53842bdfa39c" /> 

Guardamos (Ctrl+o, Enter) y salimos (Ctrl+x) 


Ahora, probamos desde firefox si realmente se nos abre la página de cada cliente cuando hacemos referencia a ellos. 

<img width="940" height="275" alt="image" src="https://github.com/user-attachments/assets/73abbe76-4e89-4de4-9d1b-a3c5612e4671" /> 

<img width="940" height="281" alt="image" src="https://github.com/user-attachments/assets/37bcb10f-3931-4c03-b409-9dd152bff693" /> 
Hemos creado 2 clientes simulados mediante directorios independientes dentro del servidor web. Para cada cliente se ha configurado un Virtual Host en Apache, permitiendo asociar cada sitio web a un subdominio específico. Y finalmente hemos verificado el funcionamiento accediendo a cada dominio de forma independiente. 


3.3 Creamos los usuarios reales del sistema.
Hasta ahora, el servidor Apache, se ejecuta bajo el usuario denominado ‘www-data’ ya que él era el único que necesitaba leer los archivos para enseñarlos en la web. Pero en un hosting real, es indispensable que cada cliente sea el dueño absoluto de su propio sitio (carpeta) y poder entrar y modificar su página web cuando quiera. Pero a la vez es necesario que ese usuario ‘**www-data**’ pueda seguir enseñando la web a los visitantes. 


Creamos cliente1 y cliente2 


**sudo adduser cliente1** 

**sudo adduser cliente2** 


Con estos dos comando hemos creado dos usuarios del sistema con su respectivo **/home/clientexX** 


Comprobamos que se han creado. 
Hemos creado 2 clientes simulados mediante directorios independientes dentro del servidor web. Para cada cliente se ha configurado un Virtual Host en Apache, permitiendo asociar cada sitio web a un subdominio específico. Y finalmente hemos verificado el funcionamiento accediendo a cada dominio de forma independiente.

3.3 Creamos los usuarios reales del sistema.
Hasta ahora, el servidor Apache, se ejecuta bajo el usuario denominado ‘www-data’ ya que él era el único que necesitaba leer los archivos para enseñarlos en la web. Pero en un hosting real, es indispensable que cada cliente sea el dueño absoluto de su propio sitio (carpeta) y poder entrar y modificar su página web cuando quiera. Pero a la vez es necesario que ese usuario ‘www-data’ pueda seguir enseñando la web a los visitantes.  

Creamos cliente1 y cliente2

sudo adduser cliente1
sudo adduser cliente2

Con estos dos comando hemos creado dos usuarios del sistema con su respectivo /home/clientexX

Comprobamos que se han creado.
<img width="782" height="93" alt="image" src="https://github.com/user-attachments/assets/7badbacd-254b-4a70-8288-b3be417fed7f" /> 

Ahora, a cada cliente le asignamos su propiedad. 


**sudo chown -R cliente1:www-data /var/www/html/cliente1** 

**sudo chown -R cliente2:www-data /var/www/html/cliente2** 


En estos momentos cada cliente pasa a ser dueño exclusivo de su sitio web y el usuario ‘**www-data**’ pertenece al grupo y permitirá acceder a los sitios web, ya deja de ser dueño de ellos. 


Finalmente, hay que reasignar permisos. 

**sudo chmod -R 750 /var/www/html/cliente1** 

**sudo chmod -R 750 /var/www/html/cliente2** 


Con estos permisos se permite que el dueño controle todo su sitio y el grupo puede acceder al sitio pero al resto no se le permite nada. 

<img width="940" height="167" alt="image" src="https://github.com/user-attachments/assets/05f3d64c-d16f-4947-8ae6-f19644ff76e5" /> 

Ahora, vamos a comprobar que Apache sigue funcionando y devuelve la página que se le solicita. 


<img width="940" height="269" alt="image" src="https://github.com/user-attachments/assets/0e838c74-6da1-4c99-8a46-1e997a468f84" /> 
<img width="940" height="269" alt="image" src="https://github.com/user-attachments/assets/59dd2412-5235-4d3a-bb0a-82747321a823" /> 

Se ha verificado el correcto funcionamiento de los sitios web tras la modificación de propietarios y permisos, comprobando que el servidor Apache mantiene acceso a los directorios de los clientes. 



## Paso 4. Transferencia de archivos y Acceso remoto.(SSH - SFTP - FTP con TSL) 


Para garantizar la confidencialidad e integridad de los datos de los clientes durante la subida de archivos, se requiere de una estricta política de conexiones seguras. El acceso por terminal se realiza mediante SSH (que de forma nativa incluye el subsistema SFTP). El servicio FTP tradicional (ProFTPD) se ha fortificado implementando FTPES (FTP explícito sobre TSL), obligando al uso de cifrado mediante certificados criptográficos y bloqueando cualquier intento de conexión en texto plano, mitigando así ataques de interceptación de red (Sniffing). 


4.1 Configurar SSH (y SFTP) 


Ejecutamos **sudo apt install openssh-server -y** 


Con ello instalamos y configuramos el servicio SSH, permitiendo el acceso seguro al servidor por parte de los usuarios creados. El servicio SSH opera como demonio en segundo plano, escuchando por defecto en el puerto 22, permitiendo conexiones seguras mediante cifrado.
SFTP es un sistema que se instala junto con SSH y que va a permitir transferir ficheros de forma segura a través del túnel que crea SSH (puerto 22). 


Comprobamos que el servicio está activo con **sudo systemctl status ssh** 
<img width="940" height="144" alt="image" src="https://github.com/user-attachments/assets/ceba5dd0-151d-4ad5-8969-c142737d8efe" /> 

Inicialmente me decía que estaba inactivo. Entonces, ejecute los siguientes comandos: 

**sudo systemctl start ssh** 

**sudo systemctl enable ssh** 

**sudo systemctl restart ssh** 


Finalmente comprobamos su funcionamiento.
**ssh cliente1@localhost** 

<img width="940" height="596" alt="image" src="https://github.com/user-attachments/assets/900bf101-4a64-4004-af0e-e8312349ee71" /> 

Ahora, comprobamos el funcionamiento de SFTP. 


**sftp cliente1@localhost** 
<img width="940" height="421" alt="image" src="https://github.com/user-attachments/assets/f656d2bc-3f90-4bfe-911c-d9e49aa0d309" /> 
**Hemos conseguido entrar en el servidor pero no en una terminal normal sino en una específica para mover archivos.** 


Nos encontramos que sftp nos comunica con nuestro directorio de trabajo **/home/cliente1** en lugar del **DocumentRoot** que es donde tenemos alojado nuestro sitio web. Para corregir esto y que nos lleve directamente al DocumentRoot cada vez que queramos realizar una transferencia de ficheros ejecutamos 

**usermod -d /var/www/html/cliente1 cliente1** 


4.2 FTP seguro con certificación TLS. 


FTP es un protocolo donde la información viaja en texto plano (usuario y passwd). Con TSL vamos a cifrar esa conexión FTP protegiendo al usuario y su contraseña además de evitar espionaje en la red. 


Instalamos ProFTPD 

**sudo apt install proftpd -y** 


Generación del certificado SSL auto-firmado y una carpeta donde guardarlo. 

**sudo mkdir -p /etc/proftpd/ssl** 

**sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/proftpd/ssl/proftpd.key -out /etc/proftpd/ssl/proftpd.crt** 


Nos hace una serie de preguntas a las que contestamos con Intro.(no ponemos nada) 


Activamos el módulo TLS en ProFTPD 


**sudo nano /etc/proftpd/proftpd.conf** 

<img width="940" height="215" alt="image" src="https://github.com/user-attachments/assets/0d68f0bd-4631-47e7-bbc8-5434bc3ba964" /> 
Guardamos (Ctrl+o, Enter) y salimos (Ctrl+x) 


Configuración de las reglas TSL. 

Se trata de indicarle cómo usar el certificado que creamos inicialmente. Para ello,

**sudo nano /etc/proftpd/tls.conf** 
<img width="940" height="358" alt="image" src="https://github.com/user-attachments/assets/addc0351-9c03-4fd9-8c25-69b03d0debe1" /> 

lo dejamos tal cual aparece en la imagen. 

Guardamos (Ctrl+o, Enter) y salimos (Ctrl+x) 


Reiniciamos ProFTPD 

**sudo sytemctl restart proftpd** 


En estos momentos el servidor debe de estar blindado y para demostrar que funciona, vamos a usar un cliente con interfaz gráfica que soporta FTP sobre SSL (FTPES) como es **FileZilla.** 


Lo instalamos con **sudo apt install filezilla -y** 

<img width="940" height="244" alt="image" src="https://github.com/user-attachments/assets/b1c18557-ddab-43ca-a5ba-af8eb151876a" /> 

En principio , no me deja conectar. 

Instale el paquete de criptografía. 

**sudo apt update && sudo apt install proftpd-mod-crypto -y** 


Volvemos a intentar conectar con el cliente1 desde Filezilla. 

<img width="940" height="521" alt="image" src="https://github.com/user-attachments/assets/b13bb8e4-a781-419c-a0a3-6be115215ce1" /> 

Se ha configurado el servidor FTP con soporte para TLS, permitiendo cifrar las comunicaciones y proteger las credenciales de los usuarios. Para ello, se ha generado un certificado SSL autofirmado y se ha habilitado el módulo correspondiente en ProFTPD. 




## Paso 5. Configuración de un servidor DNS propio (Bind9) para el dominio marisma.local. 


Hasta ahora, hemos usado /etc/hosts solución manual y poco escalable. Lo ideal es instalar y configurar un servidor DNS real que resuelva dominios automáticamente y donde se puedan crear subdominios dinámicamente. Un DNS devuelve la IP de un dominio. 


Comenzamos instalando BIND9 el servidor DNS más usado en Linux. 


**sudo apt install bind9 bin9utils bind9-doc dnsutils -y** 


Comprobamos si el servicio está corriendo. 
<img width="940" height="285" alt="image" src="https://github.com/user-attachments/assets/b5170bdb-cc0b-4328-9c64-c36c2be0b0d9" /> 

A continuación, vamos a declarar una **zona maestra** en bind9, es decir, vamos a decirle cuál es nuestro dominio y que nosotros seremos los dueños del mismo. 


Editamos el archivo de zonas locales. 

**sudo nano /ect/bind/named.conf.local** 
<img width="940" height="165" alt="image" src="https://github.com/user-attachments/assets/2dbd8e27-b968-45e1-8923-a2df648f448e" /> 
Guardamos (Ctrl+o, Enter) y salimos (Ctrl+x) 


Con esta configuración, le damos a bind el nombre de nuestro dominio, le decimos que somos los dueños absolutos del mismo y le damos la dirección exacta donde se guardarán las direcciones con todas las IP de nuestros clientes. 


Comprobamos la sintaxis de dicho fichero. 

**sudo-named-checkconf** 


Con esto, conseguimos proveer resolución de nombres dentro del servidor web sin tener que depender de manipulaciones estáticas en archivos **hosts** cliente.Hemos configurado el servidor DNS como Autoridad Maestra para la zona directa **marisma.local** editando el archivo **/etc/bind/named.conf.local** y definiendo la ruta del archivo de registros de recursos. 


Ahora, pasamos a crear el archivo de zona que contendrá los nombres de dominios con sus respectivas IP. Por suerte Bind trae un archivo de ejemplo el cual vamos a copiar y a llamarlo con el nombre que declaramos antes (**db.marisma.local**) y así solo tenemos que modificar algunas cosas en vez de crearlo de cero. 


**sudo cp /etc/bind/db.local /etc/bind/db.marisma.local** 


Abrimos el archivo recién copiado para configurar nuestros dominios. 


**sudo nano /etc/bind/db.marisma.local** 

<img width="940" height="556" alt="image" src="https://github.com/user-attachments/assets/8f3cc22b-d677-41c9-8944-dc2674324af8" /> 

Guardamos (Ctrl+o, Enter) y salimos (Ctrl+x) 


Comprobamos la sintaxis  

**sudo named-checkzone marisma.local /etc/bind/de.marisma.local** 
<img width="940" height="79" alt="image" src="https://github.com/user-attachments/assets/2519f4c5-7939-4087-947d-4c6d74379c07" /> 

Reiniciamos para que Bind9 coja todos los cambios realizados hasta el momento. 

**sudo systemctl restart bind9** 


Ahora, vamos a hacer que sea el servidor DNS el que resuelva las IP cuando se le solicite un sitio. Para ello, tenemos que empezar eliminando del fichero /etc/hosts las líneas con los nombres de los sitios junto con la IP que le asignamos (las comentaremos con #). 


**sudo nano /etc/hosts** 
<img width="940" height="360" alt="image" src="https://github.com/user-attachments/assets/f90b0e26-5f31-4670-bfd1-fb0ef8e971a4" /> 

Guardamos (Ctrl+o, Enter) y salimos (Ctrl+x) 


Editamos el archivo **/etc/resolv.conf** y le añadimos la linea **nameserver 127.0.0.1** y esto hará que el sistema consulte nuestro DNS. 

<img width="940" height="160" alt="image" src="https://github.com/user-attachments/assets/06937595-8d54-4cc6-bc28-bfa335e36fae" /> 

Hacemos pruebas para ver que el servidor DNS está funcionando perfectamente 

<img width="940" height="558" alt="image" src="https://github.com/user-attachments/assets/026aa856-1891-4ebc-8c58-7909919fe958" /> 

<img width="940" height="310" alt="image" src="https://github.com/user-attachments/assets/e2af2a35-4cb5-4036-afda-4c8d24d07619" /> 

Con esto, concluimos la instalación y configuración de un servidor DNS mediante BIND9, definiendo una zona local (**marisma.local**) que permite la resolución de subdominios asociados a los clientes. Esto sustituye el uso de archivos locales como **/etc/hosts**, proporcionando una solución escalable y más realista. 



## Paso 6. Creación de Script que automatiza tareas. 


Nosotros como administradores del servidor vamos a crear un script en nuestro directorio de trabajo. 


**sudo nano crear_cliente.sh** 
<img width="941" height="662" alt="image" src="https://github.com/user-attachments/assets/62afed18-1c42-4323-a1b4-fa2740c766e5" /> 
<img width="941" height="696" alt="image" src="https://github.com/user-attachments/assets/1f942d5e-cbcc-4ad5-b3a7-9ebe9e7fac8e" /> 
<img width="941" height="623" alt="image" src="https://github.com/user-attachments/assets/460ff54d-8ec7-4ad8-ab9f-659ba3bdff9f" /> 
Por último, le damos permisos de ejecución al script. 

**chmod +x crear_cliente.sh** 
<img width="940" height="181" alt="image" src="https://github.com/user-attachments/assets/fdf47454-5863-41e6-b752-6a0fb6cff1d3" /> 

**Al intentar darle permiso de ejecución , el sistema no me lo permitía. El problema era que el dueño del fichero era root. La solución fue cambiarle el propietario a mi usuario y ya si pude dar permiso de ejecución.** 

**El problema fue que cree el script con sudo nano y esto automáticamente se lo asigna a root. Usamos sudo solo para ejecutarlo.** 



Con este script se automatiza completamente la creación de nuevos clientes, incluyendo la configuración del servidor web, DNS, base de datos y usuarios del sistema. Además, se reducen errores y mejora la escalabilidad del sistema. 

La automatización permite replicar el proceso de forma consistente, garantizando la integridad del sistema y reduciendo la intervención manual. 


Para comprobar, antes de ejecutar el script que su sintaxis es correcta y además hace lo que realmente debe hacer se aconseja ejecutar los siguientes 3 comandos: 

**bash -n crear_cliente.sh ->** que detecta si hay fallos de sintaxis. 

**shellcheck crear_cliente.sh ->** detecta errores, malas prácticas y sugiere mejoras 

**bash -x crear_cliente.sh cliente3 ->** ejecuta mostrando paso a paso(es un debug antes de la ejecución definitiva para resolver posibles problemas que pudieran surgir en la ejecución) 


Verificar la correcta sintaxis del script usando los anteriores comandos permite detectar errores antes de su ejecución y garantiza la robustez del sistema. 

Esta validación previa de scripts es una práctica esencial en administración de sistemas para evitar fallos en entornos productivos. 



Vamos a intentar crear un nuevo cliente -> **cliente3** y para ello ejecutamos dicho script con **sudo ./crear_cliente.sh cliente3** 

<img width="940" height="306" alt="image" src="https://github.com/user-attachments/assets/401c9774-edd0-4502-ad0d-6956aa02c667" /> 
Para finalizar este punto, realizamos las pruebas pertinentes para probar que el script ha cumplido con su misión 100%. 


Comprobamos la creación del usuario **cliente3** 
<img width="940" height="56" alt="image" src="https://github.com/user-attachments/assets/3bb29737-d5d2-449f-9295-eb3bd264a80b" /> 
Directorio web. 
<img width="940" height="171" alt="image" src="https://github.com/user-attachments/assets/fd73ad13-c890-4e4d-ad0d-d2cf53410e36" /> 
Abrimos el navegador para ver si funciona la web. 
<img width="940" height="183" alt="image" src="https://github.com/user-attachments/assets/9ca4d703-20c1-4a4f-9519-5c4f6a77ea4f" /> 

DNS. 
<img width="940" height="621" alt="image" src="https://github.com/user-attachments/assets/74a96357-2b9a-4f16-97c9-70264e4470ab" /> 

Apache. 

<img width="940" height="575" alt="image" src="https://github.com/user-attachments/assets/37891ade-ea28-4664-9a3b-dd9e62ae99a0" /> 
Base de Datos. 
<img width="940" height="583" alt="image" src="https://github.com/user-attachments/assets/ade16ded-2200-4cb8-b31d-d0e2f767bace" /> 

FTP /SSH 

<img width="940" height="756" alt="image" src="https://github.com/user-attachments/assets/3ab94e11-5528-47dd-96b8-54a5833ec4fe" /> 
<img width="940" height="183" alt="image" src="https://github.com/user-attachments/assets/181051d0-3651-4bd7-8140-d97594b78dda" /> 

## Paso 7. Configurar Python en Apache. 


Para que nuestro servidor sea un entorno de hosting completo y profesional, no solo debe entender HTML puro, sino que tiene que ser capaz de ejecutar lenguajes de programación del lado del servidor. Apache no reconoce Python por defecto, por lo que tendremos que activar un “traductor” llamado CGI (Common Gateway Interface).  


Vamos a configurarlo usando este último **cliente3** que creamos de forma automática. 


Activamos el motor CGI de Apache y reiniciamos el servidor. 

**sudo a2enmod cgid** 

**sudo systemctl restart apache2** 


Damos permisos al cliente para ejecutar Python. Para ello, abrimos el archivo de configuración de nuestro cliente y le añadimos… 


**sudo nano /etc/apache2/sites-availabe/cliente3.conf** 

<img width="940" height="529" alt="image" src="https://github.com/user-attachments/assets/140cfc73-4891-4d65-848b-e1b9f8e7b1c5" /> 

Guardamos (Ctrl+o, Enter) y salimos (Ctrl+x) 


Creamos una aplicación en Python dentro de la carpeta del **cliente3** para demostrar que funciona. 


**sudo nano /var/www/html/cliente3/app.py ** 

<img width="940" height="235" alt="image" src="https://github.com/user-attachments/assets/66c02dd0-0d95-4a82-8890-6b7b74d5329f" /> 

Guardamos (Ctrl+o, Enter) y salimos (Ctrl+x) 



Le tenemos que dar permiso de ejecución al archivo .py y recargamos. 


**sudo chmod +x /var/www/html/cliente3/app.py** 

**sudo systemctl reload apache2** 



Nos vamos al navegador y vemos si funciona dicha aplicación  .py 
<img width="940" height="181" alt="image" src="https://github.com/user-attachments/assets/4e71b0b4-b43b-455b-b7a3-b43302b8b10b" /> 




































