# Hola

# Actividad 6. Configuración de un Servidor DNS maestro

Necesitamos 2 MV donde llevaremos a cabo nuestra configuración. 


**MV Ubuntu-desktop1-JB** → en ella configuraremos tanto nuestro servidor DNS primario como el servidor web “marisma.intranet”. La IP de esta máquina la cual la he configurado en estática es: **192.168.191.14/24** 


**MV Ubuntu-desktop2-JB** → en ella configuraremos nuestro servidor DNS secundario. La IP de esta máquina ya configurada en estático es: **192.168.191.128/24. Este servidor DNS secundario** le pedirá una copia de sus datos al primario (lo que se conoce como transferencia de zona) para que si el primero fallase, el secundario pueda seguir respondiendo. 

Servidor DNS primario	ns1.marisma.intranet		192.168.191.14 

Servidor DNS secundario	ns2.marisma.intranet		192.168.191.28 

Servidor web			www.marisma.intranet	192.168.191.14 

## 1. Configuración de los nombres de hosts en ambas máquinas. 
**MV1**  

<img width="790" height="232" alt="image" src="https://github.com/user-attachments/assets/dd47b463-d22a-4ec6-bd5c-014ee1a3343c" /> 


**MV2** 

<img width="797" height="227" alt="image" src="https://github.com/user-attachments/assets/02c5e594-2ce5-4840-896b-39fc0562045a" /> 

## 2. Modificar el archivo /etc/hostname para que contenga un nombre de host acorde a su finalidad en ambas máquinas.
**MV1** 

<img width="799" height="113" alt="image" src="https://github.com/user-attachments/assets/5aaeb0e0-22a5-48ad-979d-0962dd95dfcf" /> 

**MV2** 

<img width="800" height="93" alt="image" src="https://github.com/user-attachments/assets/9bab06d2-b5f5-46ba-8585-b1d0cbc00303" /> 

Para que las máquinas cojan el nuevo nombre de host que le hemos dado sin tener que reiniciar ejecutamos **sudo hostname -F /etc/hostname**  

## 3. Instalación de Bind en ambas MVs. (servidor DNS que utilizaremos) 


Para ello, ejecutamos en cada una de ellos: 

	**sudo apt update** 
  
 	**sudo apt install bind9 bind9utils bind9-doc** 
  

## 4. Ejecutamos en ambas máquinas el comando systemctl status bind9 para comprobar que dichos servidores están activos que no configurados. Ambos están OK. 
<img width="732" height="252" alt="image" src="https://github.com/user-attachments/assets/09b3ad8c-1907-4354-9df4-0f734bca27f9" /> 

## 5. Configuración del servidor DNS principal en MV1. 

Primero configuraremos el archivo **named.conf.options** con **sudo /etc/bind/named.conf.options** 

En este caso, vamos a configurar el servidor como autoritativo, es decir, solo sabe y va a contestar cuando se le pregunte por “marisma.intranet”, solo va a saber de su zona. Con ello, ganamos en seguridad y rendimiento, ya que el servidor solo trabajará para encontrar sus máquinas internas especificadas.  

<img width="886" height="294" alt="image" src="https://github.com/user-attachments/assets/2584ab07-1b26-487b-beb1-50f9cb51eb6f" />  

→ Desactivamos la recursión para que solo sea autoritativo, es decir, si la respuesta no está en sus archivos locales, le dice al cliente que no lo sabe. 


→ También se desactiva de momento cualquier transferencia de zona, para que ni siquiera el servidor secundario aún copie la agenda del servidor primario. 


Verificamos que no haya errores de sintaxis con  **named-checkconf** 

## 6. Configurar el archivo local (donde definimos nuestras propias zonas) 

Aquí definiremos las zonas que queremos controlar con este servidor primario. En bind9 a cada dominio que gestiona se le llama zona. Y para que el servidor sepa que él es el responsable de **“marisma.intranet”**, tenemos que declararlo en un archivo específico. 


Para configurar esta zona, necesitamos abrir **/etc/bind/named.conf.local** 

→ Creamos un bloque de configuración que define la zona de dominio que queremos configurar: 

	+ definimos nuestro servidor dns como maestro
	+ Apuntamos a Bind al archivo que contiene los registros de recursos que definen la zona
	+ Mantendremos nuestros archivos de zona principal en un subdirectorio ‘zones’ dentro del directorio de configuración de Bind. Llamaremos a nuestro archivo db.marisma.intranet
		sudo mkdir /etc/bind/zones
	+ Definimos la zona directa y la zona inversa para nuestro dominio.

<img width="804" height="288" alt="image" src="https://github.com/user-attachments/assets/9ba9b912-a990-4d36-b289-068ad1d3d043" /> 

Comprobar sintaxis **named-checkconf** 


→ Pasamos a crear los archivos que definirán estas zonas 


El propósito principal de un archivo de zona es asignar nombres de host y servicios a direcciones específicas. 


→ Creación del archivo de zona directa. 


	sudo nano /etc/bind/zones/db.marisma.intranet

Explicación de lo que hay dentro: 

1.	El registro SOA (Start of Authority): Indica quién es el servidor principal y quién es el responsable.
2.	El Serial: Es un número que debemos cambiar cada vez que editemos el archivo. El servidor secundario (mV2) mirará este número; si el número de mV1 es mayor que el suyo, sabrá que tiene que actualizarse.
3.	Registros NS: Indican qué servidores son los "Name Servers" (servidores de nombres) de este dominio.
4.	Registros A: Traducen un nombre a una dirección IP (IPv4).
5.	Por último, como tenemos un servidor web en uno de nuestros hosts que queremos usar para servir nuestro sitio dirigiremos las solicitudes de dicho sitio a este host; así como las solicitudes con www
Comprobar sintaxis named-checkconf

→ Creación del archivo de zona inversa. 

<img width="787" height="364" alt="image" src="https://github.com/user-attachments/assets/53eee093-380a-4026-bafa-ac08b0326861" /> 

<img width="789" height="413" alt="image" src="https://github.com/user-attachments/assets/5af19e19-6dae-44df-95f9-78012ac14748" /> 

En estos momentos, la configuración del servidor principal está completa. 

Comprobamos sintaxis con **sudo named-checkconf** 

Comprobamos el archivo de zona directa con 

**sudo named-checkzone marisma.intranet /etc/bind/zones/db.marisma.intranet** 

<img width="780" height="90" alt="image" src="https://github.com/user-attachments/assets/ae741df1-3387-44a9-a1ac-5eae3bb0ae03" /> 


Comprobamos la zona inversa con 

**sudo named-checkzone 191.168.192.in-addr.arpa /etc/bind/zones/db.192.168.191** 

<img width="760" height="80" alt="image" src="https://github.com/user-attachments/assets/7fc9e85e-11d5-4b58-a66e-589362d6612a" /> 


→ Como el ejercicio pide que configuremos en el archivo de zona los siguientes FQND: 


•	Servidor DNS: ns1 

•	Servidor ftp: ftp1 

•	Servidores de correo: mail1, mail2 

•	Servidores web: www, departamentos 

pasamos a ampliar dicho archivo de zona. 

<img width="767" height="530" alt="image" src="https://github.com/user-attachments/assets/5796509d-31c5-4c3d-8a9c-d58097170b0f" /> 
Si todas las verificaciones han salido Ok, reiniciamos el servicio Bind con **sudo service bind9 restart** 
<img width="770" height="81" alt="image" src="https://github.com/user-attachments/assets/41617bfb-207c-41dd-aa34-b12f9525d229" /> 

→ Se debe prestar especial atención comprobando los registros con **sudo tail -f /var/log/syslog** para asegurarnos de que no haya errores. 
<img width="999" height="91" alt="image" src="https://github.com/user-attachments/assets/6ecf60ba-e72c-4efe-a297-78f1a7556ddf" /> 

Hacemos algunas comprobaciones de que el servidor DNS primario responde: 
<img width="700" height="503" alt="image" src="https://github.com/user-attachments/assets/b93f8656-ccee-4f36-a002-5197f1c6a601" /> 
<img width="899" height="381" alt="image" src="https://github.com/user-attachments/assets/e84b13eb-ff9c-493c-a8e1-d7336d2384cc" /> 

## Configuración del servidor secundario. 

En el segundo servidor, solo tenemos que configurar el archivo **resolv.conf**, añadiendo la ip del servidor maestro  y diciéndole que busque la zona **marisma.intranet**. 

<img width="1004" height="440" alt="image" src="https://github.com/user-attachments/assets/63513ad5-bb0c-488b-84ad-6a7a4cf273d9" /> 

Hacemos comprobaciones.  

<img width="655" height="130" alt="image" src="https://github.com/user-attachments/assets/09ff901e-f22c-4b42-9997-47b51e16d1bb" /> 
<img width="658" height="461" alt="image" src="https://github.com/user-attachments/assets/1aeba842-7961-44b8-b831-2becd454aa12" /> 

<img width="1003" height="413" alt="image" src="https://github.com/user-attachments/assets/d7490c3d-065a-4296-98e1-01b6f61caa38" /> 

<img width="735" height="396" alt="image" src="https://github.com/user-attachments/assets/1b80a711-1b71-4b86-8c05-7136e3869e03" /> 




