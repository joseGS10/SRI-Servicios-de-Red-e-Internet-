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

El archivo de configuración principal es **named.conf**  




