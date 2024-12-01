# Portafolio CV

Proyecto de maquetado de mi portafolio utilizando HTML, CSS y JavaScript.

## Proyecto

- [Landing Page de tipo Portafolio-CV](https://Laerh.github.io/portafolio-cv/assets/hero-image-home.jpg)

# Broobe Challenge
## _Manual de instalación_

Listado de tecnologías utilizadas:

- [PHP](https://www.php.net) - para el lado del servidor.
- [Javascript](https://developer.mozilla.org/es/docs/Web/JavaScript) - para validaciones y funcionalidades en el lado del cliente.
- [Docker](https://www.docker.com/) - Despliegue de aplicación usando contenedores y armando la estructura con docker-compose.
- [Nginx](https://www.nginx.com/) - como servidor web/proxy para trabajar las peticiones.
- [Mysql](https://www.mysql.com/) - como motor de base de datos.
- [MySQL Workbench 8.0 CE](https://www.mysql.com/products/workbench/) - como gestor de base de datos.
- [Markdown](https://markdown.es/) - para el diseño del README.md.

## Installation

Descomprimir el archivo zip o clonar el repositorio de [github](https://github.com/gdklos/broobe_challenge.git).

Desplegar los contenedores creando la imagen a partir del docker-compose.

```sh
cd broobe_challenge
docker compose build
```

Una vez creado correctamente la imagen del contenedor PHP se levantan todos los servicios...

```sh
docker compose up -d
```

> Note: `Nginx` esta configurado para escuchar el puerto 80 de localhost. Verificar que no haya otra aplicación o servicio consumiendo este puerto. Por lo tanto con apuntar a la URL localhost se deberia mostrar la ruta /login.

## Configuración de Mysql

Por defecto al levantar el contenedor de Mysql se crea un esquema (luego se lo puede borrar para comprobar el funcionamiento del script) y también un usuario. Los datos de entorno de Mysql son los siguientes:


| ENVIRONMENT | VALUE |
| ------ | ------ |
| MYSQL_ROOT_PASSWORD | secret |
| MYSQL_DATABASE | dev_broobe_challenge |
| MYSQL_USER | dev_broobe_user |
| MYSQL_PASSWORD | 123456 |
| volumes | ./schemas:/var/lib/mysql |

## _Script de creación del esquema_

```sh
CREATE SCHEMA IF NOT EXISTS dev_broobe_challenge CHARACTER SET utf8mb4 COLLATE utf8mb4_bin; -- Se crea la base de datos con el conjunto de caracteres UTF-8, si ya existe el nombre da un warning.

CREATE USER 'dev_broobe_user'@'localhost' IDENTIFIED BY '123456'; -- Se crea un nuevo usuario para utilizar este esquema.

GRANT ALL PRIVILEGES ON dev_broobe_challenge . * TO 'dev_broobe_user'@'localhost'; -- Se le brinda permisos al usuario para este esquema.

USE dev_broobe_challenge; -- Nos ubicamos en el esquema a trabajar.

-- Creamos la tabla users
CREATE TABLE IF NOT EXISTS users(
	id INT NOT NULL AUTO_INCREMENT,
    name VARCHAR(255) NOT NULL,
    surname VARCHAR(255) NOT NULL,
    email VARCHAR(255) NOT NULL,
    password VARCHAR(255) NOT NULL,
    status ENUM('1','2') DEFAULT 1,
    ts_created TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    PRIMARY KEY(id)
);

-- Carga de un usuario a la tabla users utilizando como password un hash bcrypt
DROP PROCEDURE IF EXISTS PR_ADD_USER; -- Si existe, borro el PR para reemplazarlo.

DELIMITER $$
CREATE PROCEDURE PR_ADD_USER(IN p_name VARCHAR(255), IN p_surname VARCHAR(255),
	IN p_email VARCHAR(255), IN p_password VARCHAR(255))
BEGIN
    INSERT INTO users(name,surname,email,password)
    VALUES(p_name,p_surname,p_email,p_password);
END$$
DELIMITER ;

-- Se carga un nuevo usuario llamando al PR
CALL PR_ADD_USER('Gerardo','Klos','example@mail.com','$2y$10$J1o1FdFjACum1HwBP2aELOOAkFclDzZBis.DiNmEXxTb0M5b4MkXe'); -- qwe123ASD
```