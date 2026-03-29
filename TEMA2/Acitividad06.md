# Actividad 6. Configuración de un Servidor DNS maestro.

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
















