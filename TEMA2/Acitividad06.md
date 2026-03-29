# Actividad 6. Configuración de un Servidor DNS maestro.

Necesitamos 2 MV donde llevaremos a cabo nuestra configuración. 


**MV Ubuntu-desktop1-JB** → en ella configuraremos tanto nuestro servidor DNS primario como el servidor web “marisma.intranet”. La IP de esta máquina la cual la he configurado en estática es: **192.168.191.14/24** 


**MV Ubuntu-desktop2-JB** → en ella configuraremos nuestro servidor DNS secundario. La IP de esta máquina ya configurada en estático es: **192.168.191.128/24. Este servidor DNS secundario** le pedirá una copia de sus datos al primario (lo que se conoce como transferencia de zona) para que si el primero fallase, el secundario pueda seguir respondiendo. 

Servidor DNS primario	ns1.marisma.intranet		192.168.191.14 

Servidor DNS secundario	ns2.marisma.intranet		192.168.191.28 

Servidor web			www.marisma.intranet	192.168.191.14 

## 1. Configuración de los nombres de hosts en ambas máquinas. 
**MV1**
 
 
**MV2** 

<img width="797" height="227" alt="image" src="https://github.com/user-attachments/assets/02c5e594-2ce5-4840-896b-39fc0562045a" /> 

## 2. Modificar el archivo /etc/hostname para que contenga un nombre de host acorde a su finalidad en ambas máquinas.
**MV1** 
<img width="799" height="113" alt="image" src="https://github.com/user-attachments/assets/5aaeb0e0-22a5-48ad-979d-0962dd95dfcf" /> 

**MV2** 

<img width="800" height="93" alt="image" src="https://github.com/user-attachments/assets/9bab06d2-b5f5-46ba-8585-b1d0cbc00303" /> 

Para que las máquinas cojan el nuevo nombre de host que le hemos dado sin tener que reiniciar ejecutamos **sudo hostname -F /etc/hostname** 













