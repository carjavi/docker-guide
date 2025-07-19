<p align="center"><img src="https://raw.githubusercontent.com/carjavi/docker-guide/master/img/docker_logo.jpg" height="200" alt=" " /></p>

<h1 align="center">Docker Guide</h1> 
<h4 align="right">Aug 22</h4>

<p>
    <img src="https://img.shields.io/badge/OS%20-Raspbian%20GNU%2FLinux%2011%20(bulleye)-yellowgreen">
    <img src="https://img.shields.io/badge/OS-Linux%20GNU-yellowgreen">
    <img src="https://img.shields.io/badge/OS-Windows%2011-blue">
    <img src="https://img.shields.io/badge/Hardware-Raspberry%20ver%204-red">
    <img src="https://img.shields.io/badge/Node%20-V18.7.0-green">
    <img src="https://img.shields.io/badge/Python%20-V3.9.2-orange">
</p>

<br>

# Table of contents
- [Table of contents](#table-of-contents)
- [Summary](#summary)
  - [Options](#options)
  - [Samples](#samples)
  - [Notas](#notas)
- [Comandos generales](#comandos-generales)
- [Gestión de imágenes](#gestión-de-imágenes)
- [Contenedores](#contenedores)
- [Redes y volúmenes](#redes-y-volúmenes)
- [Limpieza (prune)](#limpieza-prune)
- [Repositorio de imágenes oficiales](#repositorio-de-imágenes-oficiales)

<br>

# Summary


`Docker` es una plataforma que permite empaquetar aplicaciones con todo lo necesario (bibliotecas, dependencias, configuración) en contenedores ligeros que se pueden ejecutar en cualquier entorno compatible. Incluye herramientas como Docker Engine, Docker CLI, Docker Hub e imágenes estándar definidas mediante Dockerfile. Docker es ideal para empaquetar apps y ejecutar contenedores localmente.

`Dockerfile` es un archivo de texto con instrucciones que Docker ejecuta para construir una imagen, la cual luego se usa para levantar contenedores de forma consistente y automatizada. Es un script paso a paso que describe cómo se debe preparar la imagen. cualquier persona, en cualquier entorno, puede generar la misma imagen desde el Dockerfile. Basicamente es un guión declarativo que contiene instrucciones para construir imágenes Docker desde cero. Te permite automatizar, versionar y estandarizar el empaquetado de tus aplicaciones.

`Docker Compose` es una utilidad para definir y ejecutar aplicaciones que requieren múltiples contenedores Docker, todo desde un único archivo de configuración en formato YAML ***docker-compose.yml*** o ***compose.yaml***. Permite definir en un solo lugar todos los servicios que componen tu aplicación (por ejemplo: base de datos, servidor web, cache), junto con redes, volúmenes y variables de entorno. con un solo archivo YML defines varios contenedores, redes y volúmenes. Cualquiera puede levantar el entorno con un solo comando teniendo el archi yml.

`Kubernetes` es la herramienta que orquesta esos contenedores en múltiples servidores, automatiza el escalado, el balanceo y el monitoreo. Si bien funcionaron juntos históricamente, hoy Kubernetes tiene su propio mecanismo de runtime, aunque sigue gestionando contenedores construidos con Docker.

 <br>

| Propósito        | Comando ejemplo                           |
| ---------------- | ----------------------------------------- |
| Ver la imágenes disponibles o descargadas     | `docker images` |
| Construye una imagen a partir del dockerfile en el directorio actual| `docker build . -t  <nombre imagen>`|
| Borra imagen | `docker images rm <id imagen>`|
| Lista los contenedores |`docker container ls`|
| Ejecuta una imagen (es un container corriendo) | `docker run <nombre imagen>`|
| Detener/iniciar  | `docker stop <id container> && docker start <id container>`     |
| Muestra todos los contenedores que se están ejecutando | `docker ps`|
| Ver logs         | `docker logs -f <name container>`         |
| Inicia todos los servicios del contenedor, Usa -d para detached (en segundo plano) | `docker compose up` |
| Detiene todos los servicios del contenedor, si querés borrar volúmenes usa --volumes| `docker compose down` |
| Construye las imágenes definidas en el Compose | `docker compose build`|
| Ejecuta un comando dentro de un contenedor existente | `docker exec [OPCIONES] [contenedor] [comando] [argumentos]`|
| Muestra estado actual de los servicios. | `docker compose ps`|
| Muestra logs de los servicios en ejecución | `docker compose logs` |
| Descargar imagen de hub docker | `docker pull <hub-doker-image>`    |
| Inspeccionar     | `docker inspect <name container>`          |
| Limpiar recursos | `docker system prune`                     |

## Options

`La opción -p` es usada para mapear un puerto del host (tu computadora/servidor) a un puerto dentro del contenedor, permitiendo que servicios que se ejecutan dentro del contenedor sean accesibles desde afuera, es decir, permite que servicios internos del contenedor (como servidores web, APIs, etc.) sean accesibles desde fuera de Docker.
```bash
docker run -p 8080:80 nginx
```
Con esto, si el contenedor corre un servidor web en el puerto 80 (internamente), podrás acceder desde tu máquina o red local a http://localhost:8080, que será redirigido al puerto 80 dentro del contenedor.

<br>

`La opción -d` inicia el contenedor en segundo plano (background) y te devuelve el CONTAINER ID, sin mostrar los logs ni interactuar con él directamente. Cuando querés que el contenedor siga funcionando sin bloquear tu terminal, ideal para servicios persistentes como bases de datos o web servers. Luego podés monitorear o interactuar con él usando docker logs, docker exec, etc.
```bash
docker run -d --name servidor-nginx -p 8080:80 nginx
```
El contenedor servidor-nginx queda corriendo en background. Puedes cerrar el terminal o usarlo para otras tareas. Verás que el puerto host 8080 redirige al 80 del contenedor. Para ver los logs, usarías:
```bash
docker logs -f servidor-nginx
```
Para ingresar al contenedor si necesita ejecutar comandos:
```bash
docker exec -it servidor-nginx bash
```
<br>

`La opción -it` se usa para ejecutar contenedores de manera interactiva. permitiendo enviar comandos o entrada desde tu terminal al contenedor. Basicamente permite abrir un shell interactivo dentro del contenedor
```bash
docker run -it ubuntu /bin/bash
```
Es la forma estándar para tener una sesión completa tipo bash dentro del contenedor.

<br>

```Comparativa modo interactivo (-it) vs detached (-d)```
| Modo                       | Comando              | Resultado                                 |
| -------------------------- | -------------------- | ----------------------------------------- |
| Interactivo / Primer plano | `docker run -it ...` | Ves salida y usas el terminal dentro.     |
| Detached / Segundo plano   | `docker run -d ...`  | Se ejecuta en background, terminal libre. |


<br>

## Samples

Ejecuta una imagen y redirecciona el puerto virtual 5024 a mi puerto físico 5024
```bash
docker run -p 5024:5024 <nombre imagen>
```
> :memo: **Note:* Ese contenedor va a vivir el tiempo que dura el docker run en el terminal. si se cierra el terminal se cierra el contenedor

<br>

Ejecuta una imagen y redirecciona el puerto virtual 5024 a mi puerto físico 5024 (mapear puerto)
```bash
docker run -d -p 5024:5024 <nombre imagen>
```
> :memo: **Note:* Ese contenedor va a vivir sin importar si se cierra el terminal

<br>

Ejecutar un comando simple (no interactivo). Muestra el contenido del directorio /app dentro del contenedor.
```bash
docker exec mycontainer ls -l /app 
```
```bash
docker exec -it mycontainer bash # Ejecutar un shell interactivo (ideal para debugging)
docker exec -it mycontainer sh # Esto abre un shell interactivo dentro del contenedor
```

<br>

## Notas

> :memo: **Note:* 
> * Los contenedores pueden interactuar entre ellos
> * Los contenedores son aislados, si hay un virus o malware allí no podrá salir a menos que use un puerto especifico para generar algún daño

<br>

# Comandos generales
```bash
docker --version           # Ver la versión instalada  
docker info                # Mostrar info del sistema Docker  
docker --help              # Ayuda general o por subcomando  
```

# Gestión de imágenes
```bash
docker images              # Listar imágenes locales  
docker pull <imagen>       # Descargar imagen desde Docker Hub  
docker build -t nombre .   # Crear imagen desde Dockerfile  
docker rmi <imagen>        # Eliminar imagen  
docker image prune         # Borrar imágenes no usadas  
docker tag <src> <dst>     # Etiquetar imagen (ej. repo:tag)  
docker push <repo/imagen>  # Subir imagen al repo  
```

# Contenedores
```bash
docker run --name nombre -d -p host:contenedor imagen  # Crear y ejecutar  
docker ps              # Ver contenedores en ejecución  
docker ps -a           # Ver todos (incluidos detenidos)  
docker stop <cID>      # Detener contenedor  
docker start <cID>     # Iniciar contenedor detenido  
docker rm <cID>        # Eliminar contenedor detenido  
docker exec -it <cID> bash  # Acceder al shell del contenedor  
docker logs -f <cID>   # Ver registros en tiempo real  
docker inspect <cID>   # Inspeccionar detalles del contenedor  
docker container stats # Ver uso de recursos del contenedor  
```

# Redes y volúmenes
```bash
docker network ls                  # Listar redes  
docker network create <red>       # Crear red  
docker volume ls                  # Listar volúmenes  
docker volume create <vol>        # Crear volumen  
docker cp <origen> <destino>      # Copiar archivos host <-> contenedor  
```


# Limpieza (prune)
```bash
docker system prune          # Limpiar todo lo no usado  
docker container prune       # Borrar contenedores parados  
docker image prune           # Borrar imágenes colgantes  
docker volume prune          # Borrar volúmenes no usados  
docker network prune         # Borrar redes no usadas  
```
<br>

# Repositorio de imágenes oficiales 
https://hub.docker.com/

Sample:

```bash
docker pull ubuntu # descarga la imagen oficial del "hub images"
docker run -it --entrypoint "/bin/bash" ubuntu # corre la imagen -it (permite interactuar con el contenedor)
```

<br>
<br>

---
Copyright &copy; 2022 [carjavi](https://github.com/carjavi). <br>
```www.instintodigital.net``` <br>
carjavi@hotmail.com <br>
<p align="center">
    <a href="https://instintodigital.net/" target="_blank"><img src="./img/developer.png" height="100" alt="www.instintodigital.net"></a>
</p>