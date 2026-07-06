# 🧩 DECORADORES EN TYPESCRIPT Y NESTJS

[Volver a Inicio](../../README.md)

## ¿Qué son los decoradores?

Los decoradores son funciones especiales que permiten añadir, extender o modificar el comportamiento de clases, propiedades, métodos, parámetros o accesores sin modificar directamente su implementación.

Son una característica del lenguaje TypeScript (y JavaScript, en etapa experimental) que se relacionan estrechamente con el Patrón Decorador (Decorator Pattern).

Son ampliamente utilizados en NestJS para aplicar inyección de dependencias, validaciones, middlewares, guardias, interceptores y más.

## ✅ Características

- Son funciones que pueden recibir argumentos.
- Se ejecutan antes de que se instancie la clase o se ejecute el método decorado.
- Pueden modificar, reemplazar o ampliar la lógica del elemento decorado.
- Permiten escribir código más limpio, modular y reutilizable.

## ⚠️ Estado experimental

- Los decoradores en TypeScript aún están en etapa experimental (stage 3).
- Para usarlos, debes habilitar la opción en tu "tsconfig.json":

```json
{
  "experimentalDecorators": true
}
```

## 🔍 2. emitDecoratorMetadata y reflect-metadata

- Si además de usar decoradores queremos acceder a información de tipo en tiempo de ejecución (por ejemplo, el tipo de los parámetros o el tipo de retorno), entonces también necesitamos Reflect Metadata.
- Ejemplo típico (como en NestJS o TypeORM):

```json
{
  "compilerOptions": {
    "experimentalDecorators": true,
    "emitDecoratorMetadata": true
  }
}
```

- Y en nuestro proyecto, agregamos al inicio del mismo (por ejemplo "main.ts") lo siguiente:

```ts
import "reflect-metadata";
```

## 📌 Sintaxis

Los decoradores se anteponen con @ y pueden o no recibir argumentos.

```ts
@MiDecorador()
class MiClase {}
```

## 🎯 Clasificación de Decoradores

### 1. Decoradores simples (típicos)

- Son funciones que no reciben argumentos adicionales.
- Se los invoca SIN paréntesis, ya que no reciben argumentos.

```ts
function SimpleDecorator(target: any) {
  console.log("Decorando:", target.name);
}
```

- En este caso NO recibe argumentos, "target" hace referencia al elemento que decora.

### 2. Decoradores de fábrica

- Son funciones que reciben parámetros y retornan el decorador real.
- Se los invoca CON paréntesis, ya que pueden recibir argumentos.

```ts
function LogDecorator(msg: string) {
  return function (target: any) {
    console.log(`${msg} =>`, target.name);
  };
}
```

## 📚 Tipos de Decoradores en TypeScript

1. Decorador de Clase
2. Decorador de Propiedad
3. Decorador de Método
4. Decorador de Parámetro
5. Decorador de Accesores (Getters/setters)

Los Decoradores NO pueden aplicarse a Funciones!!!

## 🧪 Ejemplos

### 🎓 Decorador de clase básico

```ts
function DemoDecorator(target: Function) {
  target.prototype.create = function (message: string) {
    console.log("Mensaje desde la clase decorada:", message);
  };
}

@DemoDecorator
class UserRepository {}

const repo = new UserRepository() as any;
repo.create("Hola mundo");
// Mensaje desde la clase decorada: Hola mundo
```

- En este ejemplo "target" hace referencia a la Clase decorada, en este caso "UserRepository".

### 🧰 Decorador de clase con lógica útil

```ts
// Decorador, que agrega el método "create" a la Clase Decorada:
function Repository(target: Function) {
  target.prototype.users = [];
  target.prototype.create = function (newUser: object) {
    this.users.push(newUser);
  };
}

// Decoramos la clase "UserRepository", agregando funcionalidad: El método "create"
@Repository
class UserRepository {
  // Sin métodos explícitos, los añade el decorador...
}

// La instancia de "UserRepository", puede hacer uso del método "create":
const users = new UserRepository() as any;
users.create({ name: "Marge" });
users.create({ name: "Homer" });

console.log(users.users); // [{ name: 'Marge' }, { name: 'Homer' }]
```

## 🧠 Decoradores en NestJS

NestJS hace uso extensivo de decoradores para su arquitectura:

- @Module() — para definir módulos.
- @Injectable() — para permitir inyección de dependencias.
- @Controller() — para definir controladores.
- @Get(), @Post(), etc. — para rutas HTTP.
- @Body(), @Param(), @Query() — para obtener datos del request.
- @UseGuards(), @UseInterceptors() — para lógica externa como validaciones o logs.

## 🔧 Decorador personalizado simple (NestJS)

```ts
export function LogClass(message: string) {
  return function (target: Function) {
    console.log(`[${message}] Clase decorada:`, target.name);
  };
}

@LogClass("INFO")
export class MyService {}
```

## 🧵 Conclusión

Los decoradores son una herramienta poderosa para:

- Aplicar metaprogramación de forma declarativa.
- Separar responsabilidades.
- Reducir código repetido.
- Hacer que NestJS sea tan expresivo y organizado.
- Es una forma elegante y de sintaxis simple para agregar o modificar funcionalidades a Clases, Métodos, Propiedades, Parámetros o Accesores.

## 🧪 Ejemplo: Decorador de método con mensaje de log

🎯 Objetivo: Crear un decorador llamado @LogMessage() que imprima un mensaje cada vez que se llama al método decorado.

1. Decorador personalizado

```ts
// src/common/decorators/log-message.decorator.ts

export function LogMessage(message: string): MethodDecorator {
  return (
    target: any,
    propertyKey: string | symbol,
    descriptor: PropertyDescriptor
  ) => {
    const originalMethod = descriptor.value;

    descriptor.value = function (...args: any[]) {
      console.log(`[LOG] ${String(propertyKey)} -> ${message}`);
      return originalMethod.apply(this, args);
    };

    return descriptor;
  };
}
```

2. Uso en un servicio

```ts
// src/app.service.ts

import { Injectable } from "@nestjs/common";
import { LogMessage } from "./common/decorators/log-message.decorator";

@Injectable()
export class AppService {
  @LogMessage("Llamando al método getHello")
  getHello(): string {
    return "Hola mundo desde NestJS!";
  }
}
```

3. Resultado al llamar el método:

```bash
[LOG] getHello -> Llamando al método getHello
```

### 🧠 Explicación paso a paso

- LogMessage(message: string): Es una función de fábrica de decoradores (puede recibir parámetros).
- Devuelve una función que actúa como decorador de método.
- descriptor.value es la función original del método.
- Se sobrescribe descriptor.value con una nueva función que:
- imprime el mensaje.
- ejecuta el método original con apply.

### ✔️ Ventajas

- No cambia el cuerpo del método.
- Puede reutilizarse en cualquier método.
- Ideal para logs, métricas, validaciones simples, etc.

---

[Volver a Inicio](../../README.md)
