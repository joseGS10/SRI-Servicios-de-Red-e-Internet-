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
<img width="1004" height="286" alt="image" src="https://github.com/user-attachments/assets/faf9dae2-925c-402e-8c7f-3c5a292a16b1" /> 

## Paso 2. Declaración de la zona principal “iesmarisma.intranet”

Editamos el archivo donde Bind guarda sus configuraciones locales 


	sudo nano /etc/bind/named.conf.local 
   <img width="739" height="127" alt="image" src="https://github.com/user-attachments/assets/d213247f-a5cc-4e31-bbd3-bae3a48377c3" /> 
   
## Paso 3. Creación del archivo de zona /etc/bind/db.iesmarisma.intranet

Para ello, vamos a utilizar como plantilla el archivo original **db.local** 


**sudo cp /etc/bind/db.local	/etc/bind/db.iesmarisma.intranet** 

**sudo nano /etc/bind/db.iesmarisma.intranet** 





