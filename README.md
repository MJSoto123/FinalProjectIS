# Proyecto Final de Ingeniería de Software: Scooby Attendance

## Trello

 [Enlace](https://trello.com/invite/b/q2yXLGNM/04a48536b6d5f02e8ac25edefeeedf0f/scooby-attendance)

<p align="center">
  <img src="https://github.com/VILLA7523/theoriginscooby/blob/main/WEB/src/public/images/logodog.png?raw=true" alt="Sublime's custom image"/>
</p>


## Práctica 9 - Estilos de Programación

### Estilo 1 - Restful
#### Descripción
REST es el principio arquitectónico subyacente de la web. Lo sorprendente de la web es el hecho de que los clientes (navegadores) y los servidores pueden interactuar de formas complejas sin que el cliente sepa nada de antemano sobre el servidor y los recursos que alberga. La restricción clave es que tanto el servidor como el cliente deben estar de acuerdo con el medio utilizado, que en el caso de la web es HTML.

Interactivo: extremo a extremo entre un agente activo (por ejemplo, una persona) y un backend.

Separación entre Cliente (interfaz de usuario) y Servidor (almacenamiento de datos)

Sin estado, como en cliente--servidor sin estado: cada solicitud del cliente al servidor debe contener toda la información necesaria para que el servidor atienda la solicitud. El servidor no puede almacenar el contexto de la interacción. El estado de la sesión está en el cliente.
Interfaz uniforme: recursos que se crean y recuperan, identificadores de recursos y representación hipermedia que es el motor del estado de la aplicación
#### Fragmento de código
Podemos ver la importación de la API, y la construcción general  de esta se encuentra [aquí](https://github.com/MJSoto123/FinalProjectIS/tree/main/Application/src/interfaces "API").
```
const indexRouter = require("./interfaces/routes/index");
const usersRouter = require("./interfaces/routes/users");
const apiRouter = require("./interfaces/routes/api");
```
### Estilo 2 - Things
#### Descripción
El problema más grande se descompone en 'cosas' que tienen sentido para el dominio del problema.
Cada 'cosa' es una cápsula de datos que expone los procedimientos al resto del mundo.
Nunca se accede a los datos directamente, solo a través de estos procedimientos.
Las cápsulas pueden reapropiarse de procedimientos definidos en otras cápsulas.
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

### Estilo 3 - Hollywood
#### Descripción
El problema más grande se descompone en entidades usando alguna forma de abstracción (objetos, módulos o similar)

Las entidades nunca son llamadas directamente para acciones.

Las entidades proporcionan interfaces para que otras entidades puedan registrar devoluciones de llamada

En ciertos puntos del cómputo, las entidades llaman a las otras entidades que se han registrado para devoluciones de llamadas.

#### Fragmento de código
La abstracción por parte de los modelos esta en la carpeta [models](https://github.com/MJSoto123/FinalProjectIS/tree/main/Application/src/domain/models "models").
Los siguientes fragmentos muestran el flujo para crear un curso, este flujo se activa cuando una profesor quiere crear un curso.
- Este el fragmento de código está en el ```professor.controller.js```, para la función ```createCourse``` donde se llama al servicio.

```
async createCourse(Course_Name, SectionID, TypeID, ProfessorID, Semestre) {
    const instanceCourseRepository = new CourseRepository(courseDb);
    const instanceCourseService = new CourseService(instanceCourseRepository);
    const result = instanceCourseService.create({
      Course_Name,
      SectionID,
      TypeID,
      ProfessorID,
      Semestre,
    });
    const data = await result.catch((err) => {
      console.log("Professor Controller Error", err);
      return null;
    });

    return data;
  }
```
- El servicio recepciona la data y llama al **repositorio**. En este caso  esta función no se encuentra alojada en el ```course.service.js```, sino que se encuentra definida en el ```data.service.js``` debido a que es una función de creación, la cual pertenece al CRUD convencional  que son manejadas por este. Sin embargo ```course.service.js``` es una extención de la ```base.service.js```, por esto las llamadas son dirigidas a ```course.service.js```.

```
async create(data) {
    const entity = await this.repository.create(data);
    if (!entity) {
      const error = new Error();
      error.status = 400;
      error.message = "Entidad no encontrada";
      throw error;
    }
    return entity;
  }
```
- Finalmente ```course.repository.js``` hace la "llamada"  al modelo o abstracción tal como lo indica el estilo Hollywood.
Cabe resaltar que debido a ser una función de creación, esta funciona del mismo modo descrito en el service, es decir esta definida en ```base.repository.js```.

```
 async create(entity) {
    return this.model.create(entity);
  }
```

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
