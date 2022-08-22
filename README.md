# Proyecto Final de Ingeniería de Software: Scooby Attendance

## Trello

 [Enlace](https://trello.com/invite/b/q2yXLGNM/04a48536b6d5f02e8ac25edefeeedf0f/scooby-attendance)

<p align="center">
  <img src="https://github.com/VILLA7523/theoriginscooby/blob/main/WEB/src/public/images/logodog.png?raw=true" alt="Sublime's custom image"/>
</p>


## Práctica 9 - Estilos de Programación

### Estilo 1 - Pipeline
#### Descripción
La mejor analogía para el estilo Pipeline es una tubería de fábrica, como las que se usan en la industria automotriz. Los automóviles se ensamblan pieza por pieza a lo largo de una "cinta transportadora" donde hay estaciones que realizan tareas individuales, como instalar el motor, poner las puertas, instalar el capó y el maletero, etc.

En programación, el estilo Pipeline se desarrolla utilizando funciones de retorno de valor. A cada tarea de un problema se le asigna una función, y la salida de la función anterior se convierte en la entrada de la función siguiente. Todos los datos se expresan como la entrada de una función o la salida de una función, excepto los datos originales que son la entrada de la primera función en la canalización.

#### Fragmento de código

### Estilo 2 - Things
#### Descripción
El problema más grande se descompone en 'cosas' que tienen sentido para el dominio del problema.
Cada 'cosa' es una cápsula de datos que expone los procedimientos al resto del mundo
Nunca se accede a los datos directamente, solo a través de estos procedimientos
Las cápsulas pueden reapropiarse de procedimientos definidos en otras cápsulas
#### Fragmento de código
El siguiente fragmento pertenece a ```login.service.js```, donde no se accede a los datos directamente, en lugar de eso se realiza una petición al repositorio.
```
async authenticate(email, password) {
    if (!email || !password) {
      const error = new Error();
      error.status = 400;
      error.message = "Email or password missing";
      throw error;
    }

    const entity = await this.repository.authenticate(email, password);

    if (!entity) {
      const error = new Error();
      error.status = 400;
      error.message = "Failed authentication";
      throw error;
    }
    return entity;
  }
```
A continuación el repositorio  realiza la consulta al modelo:
```
async authenticate(email, password) {
    return this.model.authenticate(email, password);
  }
```
### Estilo 3 - Restful
#### Descripción
REST es el principio arquitectónico subyacente de la web. Lo sorprendente de la web es el hecho de que los clientes (navegadores) y los servidores pueden interactuar de formas complejas sin que el cliente sepa nada de antemano sobre el servidor y los recursos que alberga. La restricción clave es que tanto el servidor como el cliente deben estar de acuerdo con el medio utilizado, que en el caso de la web es HTML.
Interactivo: extremo a extremo entre un agente activo (por ejemplo, una persona) y un backend
Separación entre Cliente (interfaz de usuario) y Servidor (almacenamiento de datos)
Sin estado, como en cliente--servidor sin estado: cada solicitud del cliente al servidor debe contener toda la información necesaria para que el servidor atienda la solicitud. El servidor no puede almacenar el contexto de la interacción. El estado de la sesión está en el cliente.
Interfaz uniforme: recursos que se crean y recuperan, identificadores de recursos y representación hipermedia que es el motor del estado de la aplicación
#### Fragmento de código


## Práctica 10 - Codificación Legible (Clean Code)
### 
###
### 
###
###

## Práctica 11 - Principios SOLID
###
### 
### 
