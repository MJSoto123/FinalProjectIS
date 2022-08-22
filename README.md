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
#### Fragmento de Código
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
#### Fragmento de Código
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

#### Fragmento de Código
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

### 1. Magic Numbers

#### Descripción
Un número mágico significa que estamos asignando un número sin un significado claro. A veces usamos un valor para un propósito específico y no asignamos el valor en una variable significativa. El problema es que cuando alguien trabaja con su código, esa persona no sabe el significado de ese valor directo.

#### Fragmento de Código
En el siguiente fragmento de código presentamos la configuración para  la  conección a la base de datos. Donde las variables usadas fueron extraidas de un archivo ```.env```

``` 
require("dotenv").config();
const mysql = require("mysql2");
// create the pool
const pool = mysql.createPool({
  host: process.env.HOST,
  user: process.env.USER,
  database: process.env.DATABASE,
  password: process.env.PASSWORD,
});

module.exports = pool;
```
### 2. Avoid Deep Nesting

#### Descripción
A veces usamos bucles anidados que son difíciles de entender. La forma de manejar eso es extraer todos los bucles en funciones separadas.

Supongamos que tenemos una matriz de otra matriz que contiene otra matriz y queremos el valor de la última matriz. Podemos escribir un bucle anidado que funcione para nuestros requisitos. Pero esta no es la forma adecuada. Aquí he escrito una función que puede hacer lo mismo, pero esta es mucho más limpia, más fácil, menos repetitiva, más fácil de leer y reutilizable.

#### Fragmento de Código
En la siguiente función ubicada en ```login.service.js``` que muestra un uso correcto de las practicas de codificación evitanto las anidaciónes innecesarias.

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
### 3. Meaningful Names
#### Descripción
Un nombre significativo es una de las convenciones más importantes. Utilice siempre un nombre significativo para variables, funciones y otros. Elija un nombre que exprese el significado de su propósito.
#### Fragmento de Código
A continuación mostramos la función ```updateCourse``` ubicada en el ```professor.controller.js``` donde se muestra la consistencia en el nombramiento de funciones y variables, asi como el nombramiento a entidades.

```
async updateCourse(name, section, type, semestre, id) {
    const instanceCourseRepository = new CourseRepository(courseDb);
    const instanceCourseService = new CourseService(instanceCourseRepository);

    const result = instanceCourseService.update({
      name,
      section,
      type,
      semestre,
      id,
    });
    const data = await result.catch((err) => {
      console.log("Professor Controller Error", err);
      return null;
    });
    return data;
  }
```

### 4. Use Consistent Verbs per Concept

#### Descripción
Esta es una de las convenciones de nomenclatura importantes. Si necesitamos una función CRUD, usamos ```create```, ```get``` o ```update``` con el nombre.

Si necesitamos obtener información del usuario de la base de datos, entonces el nombre de la función puede ser ```userInfo```, ```user``` o ```fetchUser```, pero esta no es la convención. Deberíamos usar ```getUser```.
#### Fragmento de Código
El fragmento de código esta ubicado en ```base.repository.js``` y muestra las funciones principales del CRUD siguiendo adecuadamente este principio, además muestra la función mensionada anteriormente para obtener información de un usuario, sin embargo las funciones descritas aquí no son para uso exclusivo del usuario, sino que son funciones genéricas para todos los modelos. Por esta razón es nombrada de forma más general y lógica como ```getByName()```
```
class BaseRepository {
  constructor(model) {
    this.model = model;
  }
  async get(id) {
    return this.model.get(id);
  }
  async getAll() {
    return this.model.getAll();
  }
  async getByName(name) {
    return this.model.getByName(name);
  }
  async create(entity) {
    return this.model.create(entity);
  }
  async update(entity) {
    return this.model.update(entity);
  }
  async delete(id) {
    return this.model.delete(id);
  }
}

module.exports = BaseRepository;
```

### 5. Capitalize SQL Special Words

#### Descripción
La interacción con la base de datos es una parte importante de la mayoría de las aplicaciones web. Si está escribiendo consultas SQL sin procesar, es una buena idea mantenerlas legibles también.

Aunque las palabras especiales de SQL y los nombres de funciones no distinguen entre mayúsculas y minúsculas, es una práctica común usar mayúsculas para distinguirlos de los nombres de tablas y columnas.

#### Fragmento de Código
El fragmento a continuación contiene un breve ejemplo donde se muestra el uso adecuado de las mayusculas al interactuar con la base de datos mediante el envio de consultas.

```
async create({ Course_Name, SectionID, TypeID, ProfessorID, Semestre }) {
    const con = connectionDb.promise();
    const data = await con.query(
      "INSERT INTO course (Course_Name,SectionID,TypeID,ProfessorID,NumEst,Semestre) VALUES (?,?,?,?,?,?)",
      [Course_Name, SectionID, TypeID, ProfessorID, 0, Semestre]
    );

    console.log("error", data);
    return data[0];
  }

  async getAll() {
    const con = connectionDb.promise();
    const data = await con.query(
      "SELECT * FROM course INNER JOIN section ON course.SectionID = section.SectionID INNER JOIN type ON course.TypeID = type.TypeID"
    );
    return data[0];
  }
```

## Práctica 11 - Principios SOLID
###
### 
### 
