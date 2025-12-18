# PRÁCTICA AWS 
# Instalación de WordPress en instancia Linux(EC2) con soporte de base de datos RDS y almacenamiento EFS. 
El objetivo de esta práctica es montar un sitio web con WordPress, una base de datos (RDS) donde WordPress guardará la información, un disco compartido en red (EFS) donde WordPress guardará imágenes y archivos y todo ello dentro de una red privada de AWS (VPC). 
**PASO_1.** Creación de la VPC 
Lo primero de todo es crear la red donde vivirá todo. Lo que haremos será crear una VPC con 2 subredes públicas y 2 subredes privadas y para ello, utilizaremos el asistente automático de AWS. 
Entramos en nuestra consola de AWS, en la barra de búsqueda escribimos VPC y en el listado de servicios que aparecen, sobre el servicio VPC, seleccionamos ‘Sus VPC’. 





