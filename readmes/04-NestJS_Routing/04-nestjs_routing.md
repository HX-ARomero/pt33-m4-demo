# Nest JS - Nest JS Routing

> El routing en NestJS define cómo las solicitudes HTTP (requests) se asignan a métodos de los controllers.

> En otras palabras, el routing conecta una URL + método HTTP con una función del controller.

[Volver a Inicio](../../README.md)

## Indice

👉 Controladores

- [1. Controladores, el punto de entrada](#controladores,-el-punto-de-entrada)
- [2. Decoradores de Métodos HTTP](#decoradores-de-métodos-http)
- [3. Parámetros de ruta](#parámetros-de-ruta)
- [4. Query params](#query-params)
- [5. Body](#body)
- [6. Rutas anidadas](#rutas-anidadas)
- [7. Prefijos globales](#prefijos-globales)
- [8. Routing y módulos](#routing-y-módulos)
- [9. Flujo del routing en NestJS](#flujo-del-routing-en-nestjs)

👉 [Guardianes en NestJS](#guardianes-en-nestjs)

## Controladores, el punto de entrada

En NestJS, las rutas se definen dentro de los controllers.

- ✅ Ruta base: /users

```ts
@Controller("users")
export class UsersController {}
```

[Volver al Indice](#indice)

---

## Decoradores de Métodos HTTP

NestJS usa decoradores para definir rutas.

- ➡️ Ruta: GET /users

```ts
@Get()
findAll() {}
```

- ➡️ Ruta: GET /users/:id

```ts
@Get(':id')
findOne(@Param('id') id: string) {}
```

📌 Decoradores más usados (Métodos HTTP):

- @Get() GET
- @Post() POST
- @Put() PUT
- @Delete() DELETE

[Volver al Indice](#indice)

---

## Parámetros de ruta

- ➡️ Ejemplo:
  - URL: /users/10
  - id = 10

```ts
@Get(':id')
findOne(@Param('id') id: string) {}
```

[Volver al Indice](#indice)

---

## Query params

- ➡️ URL: /users?page=1&limit=5

```ts
@Get()
findAll(@Query('page') page: string, @Query('limit') limit: string) {}
```

[Volver al Indice](#indice)

---

## Body

- ➡️ Se usa para recibir información en POST y PUT

```ts
@Post()
create(@Body() createUserDto: CreateUserDto) {}
```

[Volver al Indice](#indice)

---

## Rutas anidadas

- ➡️ Ruta: /users/:id/posts

```ts
@Controller("users")
export class UsersController {
  @Get(":id/posts")
  findPosts(@Param("id") id: string) {}
}
```

[Volver al Indice](#indice)

---

## Prefijos globales

En main.ts utilizamos "setGlobalPrefix():

- ➡️ Todas las rutas del back comenzarán con: /api

```ts
app.setGlobalPrefix("api");
```

[Volver al Indice](#indice)

---

## Routing y módulos

Cada controller pertenece a un módulo.

```ts
@Module({
  controllers: [UsersController],
})
export class UsersModule {}
```

👉 Sin módulo, no hay routing, el controlador debe estar conectado al proyecto, es decir que debe pertenecer a un módulo.

[Volver al Indice](#indice)

---

## Flujo del routing en NestJS

Request → Middleware → Guard → Pipe → Controller → Service → Response

📌 En NestJS:

- El controller define las rutas.
- Los decoradores definen el método HTTP.
- Los parámetros se obtienen con decoradores (@Param, @Query, @Body).
- Los módulos organizan el routing.

💡Resumen mental:

- @Controller → ruta base
- @Get/@Post → endpoints
- @Param/@Query/@Body → datos
- Module → registra controllers

[Volver al Indice](#indice)

---

## Guardianes en NestJS

- Los Guardianes (Guards) en NestJS se utilizan para controlar el acceso a las rutas.

- Su función principal es aplicar lógica de autenticación y autorización, determinando si una solicitud puede continuar hacia el controlador o debe ser rechazada.

- En otras palabras: Los Guards deciden si un usuario puede o no ejecutar una acción.

<img src="./assets/guards.png" style="display: block; margin: 20px auto 60px auto; width: 90%;" >

### ➡️ 1. Cómo funcionan

- Se ejecutan antes de que el controlador maneje la solicitud.
- Evalúan condiciones como:
  - si el usuario está autenticado
  - si tiene permisos o roles
  - si cumple ciertas reglas de negocio
- Devuelven:
  - true → permite el acceso
  - false → bloquea el acceso
  - o lanzan una excepción (UnauthorizedException, ForbiddenException, etc.)
  - También pueden devolver una Promise<boolean> o un Observable<boolean>.

### ➡️ 2. Implementación técnica

Los Guards se implementan mediante la interfaz CanActivate y el método canActivate():

```ts
import { Injectable, CanActivate, ExecutionContext } from "@nestjs/common";
import { Observable } from "rxjs";

@Injectable()
export class AuthGuard implements CanActivate {
  canActivate(
    context: ExecutionContext,
  ): boolean | Promise<boolean> | Observable<boolean> {

    // Obtenemos el Objeto Request:
    const request = context.switchToHttp().getRequest();

    // Validaciones...
    // Si la validación falla retornamos "false"

    // Si la validación es correctas:
    return true;
  }
}
```

### ➡️ 3. Idea clave

💡 Los Guards actúan como un filtro de seguridad entre la request y el controller, asegurando que solo las solicitudes autorizadas lleguen a ejecutar la lógica de negocio (Servicios).

[Volver al Indice](#indice)

---

[Volver a Inicio](../../README.md)
