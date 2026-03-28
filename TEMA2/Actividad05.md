# Cómo instalar y configurar el servidor DNS Bind9 como servidor DNS de caché o de reenvío en equipos en Ubuntu.

## Instalación de Bind 
Usaremos la MV Linux-JoseBur 

EL primer paso para implementar un servidor DNS Bind consiste en instalar su software.  

**Bind** se encuentra en los repositorios de Ubuntu. Debemos ejecutar: 

**sudo apt update**  

**sudo apt install bind9 bind9utils bind9-doc**  


## Configuración de bind como servidor DNS de almacenamiento en caché (resolver recursivo). 

Esta configuración obliga al servidor a buscar recursivamente respuestas en otros servidores DNS  cuando un cliente realiza una consulta hasta encontrar la respuesta.  

Los archivos de configuracón de Bind se guardan en **/etc/bind**  

**cd /etc/bind**  

El archivo de configuración principal es **"named.conf"**  

<img width="1004" height="463" alt="image" src="https://github.com/user-attachments/assets/e32c6167-6674-4da2-b1df-c2de1dcea083" /> 

Para un servidor DNS  con caché solo necesitamos modificar el archivo **"named.conf.options"** 

**sudo nano named.conf.options** 

Para configurar el almacenamiento en caché, primero tenemos que configurar una lista de control de acceso o ACL con direcciones IP o rango de red de confianza con los clientes que queremos que utilicen dicho servidor DNS. 


<img width="1004" height="577" alt="image" src="https://github.com/user-attachments/assets/d797d738-296b-434c-bc11-52e4a1c63914" /> 

Solo vamos a permitir el acceso a clientes de esa red. 

Una vez hemos declarado los clientes que podrán resolver sus solicitudes a través de este servidor DNS, debemos reconfigurar el bloque **options** añadiendo las siguientes lineas 

<img width="1004" height="509" alt="image" src="https://github.com/user-attachments/assets/8e178804-8b67-43b2-9c24-d3fa462dfe8f" /> 

Guardamos y salimos. Con esto hemos finalizado la configuración de Bind como servidor de caché. 

Cada vez que modificamos un archivo de configuración debemos comprobar que la sintaxis escrita es correcta ejecutando el comando **'sudo named-checkconf'** 

Una vez vemos que no hay errores reiniciamos el demonio Bind con **sudo systemctl restart bind9** 

**Probamos una consulta local.** 

<img width="1004" height="450" alt="image" src="https://github.com/user-attachments/assets/64731593-b701-48df-8dd0-b8cad4dacb5d" /> 

Ahora vamos a probar si está cacheado el servidor DNS. Y para ello, ejecutamos dos veces la misma consulta y el **‘Query time’** de la segunda consulta debe ser inferior  al de la primera, próximo a cero o cero. 

<img width="984" height="857" alt="image" src="https://github.com/user-attachments/assets/8d3cd1d0-bd22-4d54-87aa-33e7d7ae242e" /> 

**Ahora, probamos el funcionamiento desde un cliente de la misma red.** 

IP Servidor DNS : 192.168.191.46 

IP Cliente : 192.168.191.9 

<img width="842" height="931" alt="image" src="https://github.com/user-attachments/assets/bae7854a-750f-441c-92e2-c35e0bcb47eb" /> 

En la segunda consulta el tiempo de respuesta es casi cero ya que la consulta accede a la caché primero y como ya está almacenada dicha consulta de la primera vez que se consultó no hace falta salir a internet para responder. Además, el **TTL**(tiempo de vida en segundos que permanece una consulta en caché) también se debe ver reducido. 

# Configuración de Bind como servidor DNS de reenvío (forwarder).

El mismo servidor DNS lo configuraremos además como de reenvío. De esta forma cuando un cliente realiza una consulta en el servidor DNS, este la reenvía directamente al exterior. 

Volvamos a reconfigurar el archivo **"named.conf.options"** añadiendo lo siguiente. En este caso debemos modificarlo pero manteniendo el servicio recursivo al responder consultas en zonas para las que no tiene autoridad. Tenemos que incluir en un bloque las IPs de los servidores a los que queremos reenviar las solicitudes. 

<img width="1004" height="776" alt="image" src="https://github.com/user-attachments/assets/beeea6d2-c937-423a-8939-a10b01893d68" /> 

Ejecutamos **"sudo named-checkconf"** para verificar sintaxis. 

Y reiniciamos **sudo systemctl restart bind9**





