# Actividad 8. Configuración de un subdominio (o subzona)

**Ejercicio**

Configura un DNS primario para el dominio iesmarisma.intranet. Además, queremos crear un subdominio **“informatica.iesmarisma.intranet”** que resuelvan los siguientes nombres: 


Nombre de dominio principal: iesmarisma.intranet 

Nombre de hosts en el dominio principal: www, ftp, smtp 

Nombre del subdominio: informatica.iesmarisma.intranet 

Nombre de hosts en el subdominio: www, ftp, smtp 


Configura el subdominio siguiendo las instrucciones de los apuntes. Elige el método que mejor te parezca: 

1.	Creando un subdominio virtual. ****opción elegida.**
   
2.	Delegando el subdominio.


**RESOLUCIÓN** 


## Paso 1. Instalación del servidor DNS Bind9 en Ubuntu Linux

**sudo apt update** 

**sudo apt install bind9 bind9utils dnsutils** 


Esto actualiza la lista de paquetes e instalará el servidor  BIND9 y las utilidades de DNS. 


Verificamos que el servicio bin9 está corriendo:

