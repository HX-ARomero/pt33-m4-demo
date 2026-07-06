# Nest JS - Fundamentals I

[Volver a Inicio](../../README.md)

## Indice

- [Links](#links)
- [Scaffolding](#scaffolding)
- [Crear nuevo proyecto de NestJS](#crear-nuevo-proyecto-de-nestjs)
- [Módulos en NestJS](#módulos-en-nestjs)
- [Controladores en NestJS](#controladores-en-nestjs)
- [Servicios en NestJS](#servicios-en-nestjs)
- [Request, Módulos, Controladores y Servicios](#request,-módulos,controladores-y-servicios)
- [Sobre LF y CRLF](#sobre-lf-y-crlf)
- [CLI de NestJS](#cli-de-nestjs)
- [Middlewares](#middlewares)

---

## Links

- [Documentación de NestJS](https://nestjs.com/)

## Scaffolding

### Enfoque Modular

- Organiza el proyecto en carpetas que representan roles o componentes claves.
- Las carpetas contienen archivos que se centran en una función específica.

```txt
  src/
    |-- controllers/
    |-- modules/
    |-- services/
```

### Enfoque Funcional (el recomendado en Nest JS)

- Organiza tu proyecto en carpetas que representan características específicas del proyecto.

```txt
  src/
    |-- auth/
    |-- products/
    |-- users/
```

[Volver al Indice](#indice)

---

## Nest JS

- NestJS es un framework para construir aplicaciones backend eficientes y escalables con Node.js.
- Usa JavaScript moderno y está completamente construido con TypeScript (aunque también permite usar JS puro).
- Combina conceptos de Programación Orientada a Objetos (POO), Programación Funcional (FP) y Programación Funcional Reactiva (FRP).
- Funciona sobre Express (por defecto) o Fastify (opcional).
- Proporciona una arquitectura predefinida para crear aplicaciones escalables, mantenibles y testeables.
- Inspirado fuertemente en la arquitectura de Angular.
- Nest abstrae Express/Fastify, pero permite acceso directo a sus APIs y a módulos de terceros de Node.

[Volver al Indice](#indice)

---

## Crear nuevo proyecto de NestJS

- El Nest CLI (Command Line Interface) es la herramienta oficial para crear, generar y administrar proyectos NestJS.
- Se instala globalmente con:

```bash
npm i -g @nestjs/cli
```

- Verificar versión instalada:

```bash
nest -v
# o también con:
nest --version
```

- Crear nuevo proyecto de NestJS:

```bash
nest new nombre-proyecto
```

- Una vez iniciado, puedes ver tu app en http://localhost:3000/.
- También puedes iniciar un proyecto clonando desde GitHub o configurándolo manualmente.

[Volver al Indice](#indice)

---

## Módulos en NestJS

- Un MÓDULO es la unidad organizativa principal de Nest JS.
- Su función es estructurar la aplicación en bloques coherentes, facilitando la escalabilidad, el mantenimiento y la reutilización del código.
- Son Clases Decoradas con `@Module()`
- Responsabilidades:
  1. Agrupar: Reunir Controllers, Services, Providers y otros módulos que trabajan juntos.
  2. Encapsular: Limitar el alcance de lo que se exporta, solo expone lo necesario a otros módulos.
  3. Importar dependencias: Puede importar otros módulos para reutilizar sus servicios o controladores.
  4. Registrar Providers: Define qué servicios (providers) están disponibles dentro del módulo.

<img src="./assets/nest-02-01.png" style="margin: 20px 0 60px 0">

### Un Ejemplo

<img src="./assets/example.png" style="margin: 20px 0 60px 0">

[Volver al Indice](#indice)

---

## Controladores en NestJS

- Los CONTROLADOR se encarga de interactuar con el exterior.
- Su función principal es recibir las solicitudes HTTP, delegar la lógica de negocio a los servicios y devolver respuestas al cliente.
- Son Clases Decoradas con `@Controller()`
- Responsabilidades:
  1. Recibir solicitudes: Escucha rutas y métodos HTTP definidos.
  2. Validar y transformar datos (opcional): Mediante el uso de pipes o DTOs para asegurar que los datos de entrada sean correctos.
  3. Delegar la lógica de negocio a los Servicios: El rol del Controlador es coordinar, no resolver el problemas.
  4. Devolver respuesta: Retorna el resultado al cliente (normalmente en formato JSON).

<img src="./assets/nest-02-02.png" style="margin: 20px 0 60px 0">

[Volver al Indice](#indice)

---

## Servicios en NestJS

- Los SERVICIO contiene la lógica de negocio y el procesamiento de datos.
- Es el lugar donde se implementan las reglas, cálculos y operaciones principales del sistema, es el “cerebro” de la aplicación.
- Son Clases Decoradas con `@Injectable()`
- Responsabilidades:
  1. Procesar la lógica del negocio: Cálculos, validaciones de negocio, transformación de datos y reglas específicas del sistema.
  2. Acceder a los Repositorios: Puede llamar a repositorios, modelos, u otros servicios.
  3. Reutilizar funcionalidad: Puede ser inyectado en distintos controladores o servicios.
  4. Mantener el código limpio: Separa la lógica de la capa de presentación (controlador).

- En Resumen:
  - Los Módulos encapsulan código, permitiendo modularizar, y organizar el proyecto.
  - Los Controladores "coordinan" y ejecutan sercicios según el path y método http recibido.
  - Los Servicios son el "cerebro" de la aplicación, y maneja la lógica de negocio.

<img src="./assets/nest-02-03.png" style="margin: 20px 0 60px 0">

[Volver al Indice](#indice)

---

## Inyección de Dependencias

- La inyección de dependencias es un patrón de diseño que consiste en proveer a una clase los objetos que necesita, en lugar de que ella misma los cree.
- En lugar de crear dependencias con new, se las inyecta desde afuera.

<img src="./assets/dependencies.png" style="margin: 20px 0 60px 0">

[Volver al Indice](#indice)

---

## Request, Módulos, Controladores y Servicios

### GET a "/"

<img src="./assets/nest-02-04.png" style="margin: 20px 0 60px 0">

### Módulo Users - GET a "/users"

<img src="./assets/nest-02-05.png" style="margin: 20px 0 60px 0">

### El Objeto Request

- En JavaScript / Node.js / NestJS (y Express), el objeto Request representa la petición HTTP que hace el cliente al servidor.
- Veamos un ejemplo del objeto req y sus propiedades mas importantes

- GET a la ruta: `http://localhost:3000/users/10?name=Homero&age=39`
  - El Objeto `req`:

```ts
const req = {
  // Método HTTP:
  method: "GET",

  // URL original completa (no cambia nunca):
  originalUrl: "/users/10?name=Homero&age=39",

  // URL relativa al router actual (puede cambiar):
  url: "/10?name=Homero&age=39",

  // Path sin protocolo, host y query params (ruta actual):
  path: "/10",

  // Prefijo del router (donde se montó el router)
  baseUrl: "/users",

  // Información de la ruta que se está ejecutando:
  route: {
    path: "/:id",     // ruta definida en el controller/router
    methods: {
      get: true
    }
  },

  // Parámetros de ruta (dinámicos):
  params: {
    id: "10" // viene de /users/:id
  },

  // Query params (después del ?):
  query: {
    name: "Homero",
    age: "39"
  },

  // Body de la petición (en GET normalmente vacío):
  body: {},

  // Headers HTTP:
  headers: {
    host: "localhost:3000",
    connection: "keep-alive",
    user-agent: "Mozilla/5.0",
    accept: "*/*",
    content-type: "application/json",
    // Si se envía un Token por "Bearer Auth":
    authorization: "Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."
  },

  // IP del cliente:
  ip: "::1", // localhost (IPv6)
};

```

#### Diferencia entre req.url y req.originalUrl

- ✅ req.url
  - Es la URL relativa al router actual (puede cambiar si hay middlewares o routers).
  - Es la parte de la URL que Express está procesando en ese momento (en el router actual).
- ✅ req.originalUrl
  - Es la URL original completa tal como llegó al servidor.
  - Nunca cambia, aunque pases por routers o middlewares.

[Volver al Indice](#indice)

---

## Sobre LF y CRLF

> LF (Line Feed) y CRLF (Carriage Return + Line Feed) son secuencias de control utilizadas en sistemas informáticos para representar el final de una línea de texto.

### LF (Line Feed)

Es un carácter de control que se utiliza para indicar el final de una línea de texto en sistemas Unix y Unix-like (como Linux y macOS). En ASCII, el código de LF es 10 (0x0A en hexadecimal). Cuando se encuentra un LF, el cursor de escritura se mueve a la siguiente línea.

### CRLF (Carriage Return + Line Feed)

Es una combinación de dos caracteres de control que se utiliza para indicar el final de una línea de texto en sistemas Windows y DOS. El Carriage Return (retorno de carro) es un carácter que mueve el cursor al principio de la línea, y el Line Feed es el carácter que indica el final de la línea. En ASCII, el código de Carriage Return es 13 (0x0D) y el código de Line Feed es 10 (0x0A).

### En resumen

LF se utiliza en sistemas Unix para indicar el final de una línea de texto, mientras que CRLF se utiliza en sistemas Windows y DOS para el mismo propósito. Estas diferencias en el manejo de los saltos de línea a veces pueden causar problemas de compatibilidad al trabajar con archivos de texto en diferentes sistemas operativos.

### ✅Solución: `endOfLine: 'auto'` en `eslint.config.mjs`:

- Deja que **Prettier detecte el estilo de línea según el sistema operativo** (LF en Unix/macOS/Linux, CRLF en Windows).
- Al incluirlo como regla de ESLint (`'prettier/prettier'`), **unifica formato y lint** y evita errores.
- Esta opción **minimiza conflictos** al trabajar en equipo en diferentes sistemas.
- Es ideal para proyectos multiplataforma y colaborativos.
- En ARCHIVO "eslint.config.mjs":

```js
// ARCHIVO eslint.config.mjs
// ----- IMPORTS -----

export default tseslint.config(
  // ----- ----- ----- -----
  {
    rules: {
      "@typescript-eslint/no-explicit-any": "off",
      "@typescript-eslint/no-floating-promises": "warn",
      "@typescript-eslint/no-unsafe-argument": "warn",
      "prettier/prettier": [
        "error",
        {
          endOfLine: "auto",
        },
      ],
    },
  },
);
```

[Volver al Indice](#indice)

---

## CLI de Nest JS

- El Nest CLI (Command Line Interface) es la herramienta oficial para crear, generar y administrar proyectos NestJS.

### Inicializar proyecto de Nest (Sin CLI Global)

```bash
npx @nestjs/cli new nest-demo

cd nest-demo

# Para correr proyecto de Nest:
npm run start
# Para actualizar servidor ante un cambio en el código
npm run start:dev
# Cortar servidor:
"Ctrl + c"
```

### Instalación de CLI de forma global

```bash
npm install -g @nestjs/cli

# Obtener listado de dependencias globales:
npm list -g --depth=0

# Desinstalar una dependencia global:
npm uninstall -g @nestjs/cli
```

### Inicializar proyecto de Nest

```bash
nest new nest-demo

cd nest-demo

# Para correr proyecto de Nest:
npm run start
# Para actualizar servidor ante un cambio en el código
npm run start:dev
# Cortar servidor:
"Ctrl + c"
```

### Comandos Generales

```bash
nest generate <comando>
nest g <comando>
```

### Ayuda

```bash
nest -h
nest g -h
nest g s nombre -h
```

### Componentes

```bash
# Crear un Controlador:
nest g co <path/nombre>

# Crear un Decorador:
nest g d <path/nombre>

# Crear un Guardián:
nest g gu <path/nombre>

# Crear un Interceptor:
nest g in <path/nombre>

# Crear un Módulo:
nest g mo <path/nombre>

# Crear un Pipe:
nest g pi <path/nombre>

# Crear un Service:
nest g s <path/nombre>

# Crear un Recurso Completo:
nest g resource <path/nombre>

# Crear un Middleware:
nest g middleware <path/nombre>
```

### Banderas (Flags)

```bash
# Crear en modo plano:
nest g co <path/nombre> --flat

# No crear archivo de Test:
nest g co <path/nombre> --no-spec
```

[Volver al Indice](#indice)

---

## Middlewares

- Un Middleware es una función de paso (intermediaria)
  - Tiene acceso a "Req" y "Res"
  - Se ejecuta previo a que la solicitud llegue al Controlador
- Pueden crearse en una carpeta "./src/middlewares", ó dentro de la carpeta de la entidad correspondiente si forma parte exclusiva de un módulo.
- En Nest JS puede crearse de forma manual o mediante la CLI

[Volver al Indice](#indice)

---

[Volver a Inicio](../../README.md)
