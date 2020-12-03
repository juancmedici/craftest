## Arquitectura:

### AWS

En el diagrama de red tenemos una Region en la cual voy a desplegar mi aplicación web, para llevar a cabo este procedimiento necesito agregar una VPC en donde van a vivir los componentes elegidos y clonados para que la aplicación escale según demanda.  
Definimos una Internet Gateway para darle acceso a internet a la VPC y acceso a los clientes. La Internet Gateway va a estar conectada al Application Load Balancer porque voy a necesitar distribuir la carga entre mis diferentes aplicaciones.  
Defino una subnet por cada componente o aplicación y las duplico para tenerlas disponibles en dos Availability Zones.  
Creo un Kubernetes Cluster, el cual va a ser provisto por un servicio de Amazon ECS y voy a colocar las dos instancias del cluster en un Autoscaling Group para que escale dependiendo de la carga.  
Defino los servicios externos dentro de la arquitectura por lo tanto ambas DB tienen que estar dentro de las Availability Zones.


### Despliegue Manual:

Para realizar la compilación y el despliegue manual de esta aplicación, necesitamos tres archivos:
1. Dockerfile (file_path: ./backend/Dockerfile).
2. Dockerfile (file_path: ./frontend/Dockerfile).
3. docker-compose.yml (file_path: ./docker-compose.yml).

#### Requisitos:
#### Instalar Docker

Descargar el archivo '.deb' (Ubuntu) del siguiente link:

~~~

https://download.docker.com/linux/ubuntu/dists/

~~~

Luego, instalar el paquete:

~~~~

sudo dpkg -i /path/to/package.deb

~~~~