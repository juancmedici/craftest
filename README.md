##### Arquitectura:

#### AWS

En el diagrama de red tenemos una Region en la cual voy a desplegar mi aplicación web, para llevar a cabo este procedimiento necesito agregar una VPC en donde van a vivir los componentes elegidos y clonados para que la aplicación escale según demanda.
Definimos una Internet Gateway para darle acceso a internet a la VPC y acceso a los clientes. La Internet Gateway va a estar conectada al Application Load Balancer porque voy a necesitar distribuir la carga entre mis diferentes aplicaciones.
Defino una subnet por cada componente o aplicación y las duplico para tenerlas disponibles en dos Availability Zones.
Creo un Kubernetes Cluster, el cual va a ser provisto por un servicio de Amazon ECS y voy a colocar las dos instancias del cluster en un Autoscaling Group para que escale dependiendo de la carga.
Defino los servicios externos dentro de la arquitectura por lo tanto ambas DB tienen que estar dentro de las Availability Zones.