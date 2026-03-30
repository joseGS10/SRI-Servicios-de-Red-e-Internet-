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

<img width="753" height="448" alt="image" src="https://github.com/user-attachments/assets/9fe258dd-5c14-4bdc-8c72-ea00df5b740e" /> 

Como el subdominio a configurar será uno virtual, hemos indicado todos los equipos de este subdominio virtual apuntando a la misma IP. 


Verificamos que no haya errores de sintaxis: 

•	Para la configuración general ejecutamos **sudo named-checkconf** 

•	Para el archivo de zona ejecutamos 


**sudo named-checkzone iesmarisma.intranet /etc/bind/db.iesmarisma.intranet** 

<img width="916" height="107" alt="image" src="https://github.com/user-attachments/assets/e8db1e9f-6f9c-466c-9f5c-07e73a923cd4" /> 

A continuación, reiniciamos Bind para que cargue la nueva zona  

<img width="456" height="47" alt="image" src="https://github.com/user-attachments/assets/0b553c55-44c9-4cc1-87fd-dac8a15d7ce7" /> 

Revisamos el log por posibles errores 

<img width="919" height="334" alt="image" src="https://github.com/user-attachments/assets/6eda9798-c9c0-42f2-b444-78c05bf6ba19" /> 

Finalmente, hacemos una serie de comprobaciones. 

<img width="570" height="181" alt="image" src="https://github.com/user-attachments/assets/71ceca97-2a55-4bb3-9ddd-18512e79274b" /> 

<img width="802" height="502" alt="image" src="https://github.com/user-attachments/assets/a48a042f-1940-48a5-9991-b15dfb0a271c" /> 

## Paso 4. Subdominio virtual. 


Para configurarlo basta con abrir el archivo de zona que ya teníamos de antes y añadir en él nuevas entradas para el subdominio y cambiar el valor del serial de 2 a 3 para que cuando le pedimos a Bind que recargue la configuración, lo primero que hará es mirar ese número. Si el Serial es idéntico al que ya tiene en la memoria, asume que el archivo no ha cambiado y directamente ignora las nuevas líneas. Al cambiarlo a 3, forzamos al servicio a leer el archivo completo, incluyendo el nuevo subdominio que acabas de añadir. 


**sudo nano /etc/bind/db.iesmarisma.intranet** 

<img width="991" height="688" alt="image" src="https://github.com/user-attachments/assets/5c20e29a-77fd-4a22-9992-1d098758bfbb" /> 

Verificamos y reiniciamos. 

<img width="723" height="136" alt="image" src="https://github.com/user-attachments/assets/da50a256-021b-4d00-9a9b-3c6fee6219e8" /> 

Verificamos el reconocimiento del subdominio. 

<img width="1004" height="510" alt="image" src="https://github.com/user-attachments/assets/0e9d5ad4-5137-49db-b243-08355fdc4bd6" /> 

