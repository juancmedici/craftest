## Arquitectura:

### AWS: Descripción

En el diagrama de red tenemos una Region en la cual voy a desplegar mi aplicación web, para llevar a cabo este procedimiento necesito agregar una VPC en donde van a vivir los componentes elegidos y clonados para que la aplicación escale según demanda.  
Definimos una Internet Gateway para darle acceso a internet a la VPC y acceso a los clientes. La Internet Gateway va a estar conectada al Application Load Balancer porque voy a necesitar distribuir la carga entre mis diferentes aplicaciones.  
Defino una subnet por cada componente o aplicación y las duplico para tenerlas disponibles en dos Availability Zones.  
Creo un Kubernetes Cluster, el cual va a ser provisto por un servicio de Amazon ECS y voy a colocar las dos instancias del cluster en un Autoscaling Group para que escale dependiendo de la carga.  
Defino los servicios externos dentro de la arquitectura por lo tanto ambas DB tienen que estar dentro de las Availability Zones.


### Despliegue Local:

Para realizar la compilación y el despliegue local de esta aplicación, necesitamos tres archivos:
1. Dockerfile (file_path: ./backend/Dockerfile).
2. Dockerfile (file_path: ./frontend/Dockerfile).
3. Dockerfile (file_path: ./custom-nginx/Dockerfile).
4. docker-compose.yml (file_path: ./docker-compose.yml).

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

#### Instalar Docker Compose

Luego de instalar Docker, necesitamos instalar Docker Compose para correr nuestro docker-compose.yml

~~~
sudo curl -L https://github.com/docker/compose/releases/download/1.21.2/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose
~~~

Luego, configuramos los permisos de ejecución:

~~~
sudo chmod +x /usr/local/bin/docker-compose
~~~

#### Ejecución:

Una vez que tenemos todo instalado y configurado, ejecutamos el despliegue.  
1. Nos paramos sobre el directorio raíz y corremos el comando:

~~~
docker-compose up
~~~

2. Una vez que termina de correr el comando, vamos al navegador:
- http://0.0.0.0:3000/ (React)
- http://0.0.0.0:8080/ (Nginx)


### Despliegue en AWS:

"Despliegue en AWS de prueba".  
Para realizar el despliegue en AWS de esta aplicación, necesitamos dos archivos:
1. docker-compose.yml (file_path: ./sample/docker-compose.yml).
2. ecs-params.yml (file_path: ./sample/ecs-params.yml).

#### Requisitos previos:

1. Creación de un usuario IAM en la cuenta de AWS.
2. Crear un grupo y agregar el usuario creado en el paso anterior.
3. Crear Key Pairs para poder acceder a la instancia a través de SSH.
4. Cambiar los permisos de nuestro my-key-pair.pem:

~~~
chmod 400 my-key-pair-region_name.pem
~~~

5. Crear una VPC o utilizar la VPC predeterminada.
6. Crear un Security Group y configurar las reglas de entrada.
7. Instalar AWS CLI y configurar las Acces Key.
8. Descargar e instalar ECS CLI:

~~~
sudo curl -Lo /usr/local/bin/ecs-cli https://amazon-ecs-cli.s3.amazonaws.com/ecs-cli-linux-amd64-latest
~~~

- Verificar ECS CLI utilizando firmas PGP:

~~~
sudo apt-get install gpg
~~~

- Recuperar clave pública PGP:

~~~
gpg --keyserver hkp://keys.gnupg.net --recv BCE9D9A42D51784F
~~~

- Descargar las firmas PGP de ECS CLI:

~~~
curl -Lo ecs-cli.asc https://amazon-ecs-cli.s3.amazonaws.com/ecs-cli-linux-amd64-latest.asc
~~~

- Verificar la firma:

~~~
gpg --verify ecs-cli.asc /usr/local/bin/ecs-cli
~~~

- Configurar permisos de ejecución al binario:

~~~
sudo chmod +x /usr/local/bin/ecs-cli
~~~

8. Configurar la CLI de ECS:

- Crear la configuración del cluster:

~~~
ecs-cli configure --cluster ec2-devops --default-launch-type EC2 --config-name ec2-devops --region us-east-1
~~~

- Crear un perfil utilizando una clave de acceso y una clave secreta:

~~~
ecs-cli configure profile --access-key AWS_ACCESS_KEY_ID --secret-key AWS_SECRET_ACCESS_KEY --profile-name ec2-devops-profile
~~~

#### Ejecución:

- Crear un cluster:

~~~
ecs-cli up --keypair my-key-pair --capability-iam --size 2 --instance-type t2.medium --cluster-config ec2-devops --ecs-profile ec2-devops-profile
~~~

Una vez creado el cluster, podemos verificar si funciona nuestra aplicación de prueba.

- Ver los contenedores en ejecución:

~~~
ecs-cli ps --cluster-config ec2-devops --ecs-profile ec2-devops-profile
~~~


- Finalizada la prueba, para no incurrir cargos adicionales, debemos limpiar los recursos:

~~~
ecs-cli compose service rm --cluster-config ec2-devops --ecs-profile ec2-devops-profile
~~~

- Por último, cerrar el cluster:

~~~
ecs-cli down --force --cluster-config ec2-devops --ecs-profile ec2-devops-profile
~~~