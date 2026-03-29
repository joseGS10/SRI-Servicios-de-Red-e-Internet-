# Actividad 6. Configuración de un Servidor DNS maestro.

Necesitamos 2 MV donde llevaremos a cabo nuestra configuración. 


**MV Ubuntu-desktop1-JB** → en ella configuraremos tanto nuestro servidor DNS primario como el servidor web “marisma.intranet”. La IP de esta máquina la cual la he configurado en estática es: **192.168.191.14/24** 


**MV Ubuntu-desktop2-JB** → en ella configuraremos nuestro servidor DNS secundario. La IP de esta máquina ya configurada en estático es: **192.168.191.128/24. Este servidor DNS secundario** le pedirá una copia de sus datos al primario (lo que se conoce como transferencia de zona) para que si el primero fallase, el secundario pueda seguir respondiendo. 

Servidor DNS primario	ns1.marisma.intranet		192.168.191.14 

Servidor DNS secundario	ns2.marisma.intranet		192.168.191.28 

Servidor web			www.marisma.intranet	192.168.191.14 

1. Configuración de los nombres de hosts en ambas máquinas.



