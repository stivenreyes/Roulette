# Prueba Clean Code - Juego de Ruleta

Esta aplicación es un API que representa una ruleta de apuestas.

**Proyecto hecho en Java 8 y Spring Boot**

## Tabla de contenido

- [Requisitos para ejecutar la aplicación](#requisitos-para-ejecutar-la-aplicación).
- [IMPORTANTE](#importante).
- [Ejecutar aplicación](#ejecutar-aplicación).
- [Dockerización](#Dockerización)
- [API](#api).

### Requisitos para ejecutar la aplicación

- [Java 1.8 JDK](https://www.oracle.com/java/technologies/javase/javase-jdk8-downloads.html "Java 1.8 JDK")
- [IntelliJ IDEA](https://www.jetbrains.com/es-es/idea/ "IntelliJ IDEA") o Spring Tools Suite 4
- [Postman](https://www.postman.com/ "Postman") Para ejecutar pruebas al API

### IMPORTANTE

Tener una base de datos mysql en la cual pueda crear las tablas para el proyecto.
El script sql se encuentra en la siguiente ruta: `src/main/resources/database.sql`
Después de crear la base de datos, se crea primero la tabla de roulette y luego la tabla de bet.

No olvide actualizar los properties de la aplicación con los cambios para conectar correctamente a la base de datos (`src/main/resources/application.properties`)

## Ejecutar aplicación

1. Clonar proyecto

2. Importar proyecto en Intellij IDEA u otro y ejecutar la aplicacion.
Tambien puede generar el package del proyecto.
Recuerde la información previamente descrita en cuanto a la base de datos (Ver IMPORTANTE)

La aplicación se ejecutara en el **PUERTO 8080**

# Dockerización

#### Crear imagen de docker

Para crear una imagen de docker de la aplicacion, ejecute el siguiente comando de maven en la raiz del proyecto: 
*Si no tiene maven configurado en el equipo puede usar la terminal de Intellij*

`mvn package docker:build`

![](https://i.ibb.co/3RGyHRk/Captura3.png)

####  Crear contenedor de la aplicación

`docker run -d --name roulette-api -e MYSQL_HOST=192.168.99.100 -e MYSQL_USERNAME=root -e MYSQL_PASSWORD=my-secret-pw -p 8080:8080 stivenreyes/roulette:latest`

Donde MYSQL_HOST, MYSQL_USERNAME y MYSQL_PASSWORD son parametros con configuraciones de la base de datos.

## API

### Crear una ruleta [GET] `/roulette/new`

Endpoint que crea una ruleta para empezar a jugar.

Resultado:
```json
{
    "status": "OK",
    "data": 9, 
    "error": null
}
```
- **status:** Resultado de la operación
- **data:** Id de la ruleta para posteriores peticiones.
- **error:** Mensaje de error si este ocurre

### Abrir una ruleta [GET] `/roulette/open/1` 
**Donde 1 es el id de la ruleta creada previamente**

Permite las posteriores peticiones de apuestas 

Resultado:
```json
{
    "status": "OK",
    "data": "successful operation",
    "error": null
}
```
- **status:** Resultado de la operación
- **data:**  Mensaje describiendo el resultado de la operación
- **error:** Mensaje de error si este ocurre

### Crear una apuesta a un NÚMERO [POST] `/bet/new/number` 
Endpoint de apuesta a un número (los números válidos para apostar son del 0 al 36)

**Body:**
```json
{
    "idRoulette": 1,
    "number": 7,
    "money": 10000
}
```
- **idRoulette:** Number | Id de la ruleta
- **number:** Number | Número al que se apostara
- **money:** Number | cantidad de dinero apostar | Minimo: 1 Máximo: 10000

**Resultado:**
```json
{
    "status": "OK",
    "data": 1,
    "error": null
}
```
- **status:** Resultado de la operación
- **data:** Id de la apuesta
- **error:** Mensaje de error si este ocurre

### Crear una apuesta a un COLOR [POST] `/bet/new/color` 
Endpoint de apuesta a un color (rojo o negro)
**Body:**
```json
{
    "idRoulette": 1,
    "color": "red",
    "money": 1
}
```
- **idRoulette:** Number | Id de la ruleta
- **number:** String | Color al que se apostara | valores disponibles: **BLACK** y **RED**
- **money:** Number | cantidad de dinero apostar | Minimo: 1 Máximo: 10000

**Resultado:**
```json
{
    "status": "OK",
    "data": 1,
    "error": null
}
```
- **status:** Resultado de la operación
- **data:** Id de la apuesta
- **error:** Mensaje de error si este ocurre

### Cerrar ruleta [GET] `/roulette/close/1`
**Donde 1 es el id de la ruleta a cerrar**

Este endpoint devuelve el resultado de las apuestas hechas desde su apertura hasta el cierre.
Resultado:
```json
{
    "status": "OK",
    "data": [
        {
            "idBet": 1,
            "money": 0,
            "result": "LOSER",
            "betType": "NUMBER"
        }
    ],
    "error": null
}
```
- **status:** Resultado de la operación
- **data:** Lista con el resultado de las apuestas
	-  **idBet**: Id de la apuesta
	-  **money:** Cantidad de dinero ganada
	-  **result:** WINNER si la apuesta fue ganadora y LOSER si la apuesta fue perdida
	-  **betType:** El tipo de apuesta que se realizó
- **error:** Mensaje de error si este ocurre

### Obtener todas las ruletas [GET] `/roulette/all`

Endpoint de listado de ruletas creadas con sus estados (abierta o cerrada)
Resultado:
```json
{
    "status": "OK",
    "data": [
        {
            "id": 1,
            "state": "CLOSED",
            "randomNumber": 1
        }
    ],
    "error": null
}
```
- **status:** Resultado de la operación
- **data:** Lista de todas las ruletas
	-  **id**: Id de la ruleta
	-  **state:** Estado de la ruleta | CREATED: la ruleta fue creada - OPEN: la ruleta está abierta - CLOSED: la ruleta esta cerrada 
	-  **randomNumber:** si la ruleta esta cerrada, este campo tiene el número aleatorio que lanzo la ruleta
- **error:** Mensaje de error si este ocurre
