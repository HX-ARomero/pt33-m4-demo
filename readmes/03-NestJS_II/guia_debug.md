# 🧠 Guía Completa de Debug en NestJS

> Esta guía presenta prácticas básicas y técnicas avanzadas de depuración en NestJS. No pretende ser exhaustiva, sino servir como una puerta de entrada al razonamiento detrás del debug y abrir nuevas perspectivas para entender cómo analizar y resolver errores de forma sistemática.

> Algunos conceptos abordados son más avanzados y quizás no los utilices de inmediato, pero te servirán como base para futuros proyectos y desafíos, incluidas los próximos homeworks.

[Volver a Inicio](../../README.md)

## Indice

- [1. Qué es Debug](#qué-es-debug)
- [2. Tipos de errores comunes en NestJS](#tipos-de-errores-comunes-en-nestjs)
- [3. Herramientas básicas de debug](#herramientas-básicas-de-debug)
- [4. Debug paso a paso con breakpoints](#debug-paso-a-paso-con-breakpoints)
- [5. Debug en capas de NestJS](#debug-en-capas-de-nestjs)
- [6. Debug en autenticación JWT](#debug-en-autenticación-jwt)
- [7. Debug en Base de Datos](#debug-en-base-de-datos)
- [8. Debug con variables de entorno](#debug-con-variables-de-entorno)
- [9. Manejo de errores y Exception Filters](#manejo-de-errores-y-exception-filters)
- [10. Debug avanzado](#debug-avanzado)
- [11. Estrategias de debug](#estrategias-de-debug)
- [12. Resumen Debug en NestJS](#resumen-Debug-en-nestjs)

---

## Qué es Debug

- Debug es el proceso de identificar, analizar y corregir errores en una aplicación.
- En NestJS, el debug puede hacerse a distintos niveles:
  - Enrutamiento (controladores)
  - Lógica de negocio (servicios)
  - Infraestructura (DB, APIs externas)
  - Framework (pipes, guards, interceptors, middleware)
  - Runtime (Node.js)

[Volver a Indice](#indice)

---

## Tipos de errores comunes en NestJS

### 🔹Errores de compilación (TypeScript)

- Tipos incorrectos
- Interfaces mal definidas
- Imports mal configurados

### 🔹Errores en runtime

- Variables undefined/null
- Excepciones no controladas
- Promesas sin await

### 🔹Errores de arquitectura

- Dependencias circulares
- Providers mal registrados
- Módulos mal configurados

### 🔹Errores de integración

- Base de datos
- APIs externas
- Variables de entorno

[Volver a Indice](#indice)

---

## Herramientas básicas de debug

### console.log (JavaScript / TypeScript)

📌 ¿Qué es console?

- console es un objeto global de JavaScript que permite imprimir información en la consola para depurar.

📌 Métodos más usados del objeto console:

```ts
console.log("Mensaje"); // Debug básico, valor de variables, seguir flujo, etc
console.info("Información"); // Indicar eventos importantes que no son errores
console.warn("Advertencia"); // Situaciones sospechosas no críticas, futuros errores
console.error("Error"); // Errores reales, excepciones, fallos críticos
console.table([{ id: 1, name: "Ariel" }]); // Visualización de arrays
console.time("proceso"); // Inicia un temporizador con el nombre 'proceso'
console.timeEnd("proceso"); // Detiene el temporizador iniciado con console.time('proceso') y muestra el tiempo transcurrido.
console.trace("Traza"); // Muestra la pila de llamadas (stack trace), permite saber desde dónde se llamó una función y entender el flujo de ejecución
```

📌 Buenas prácticas

- No dejar logs en producción sin control
- Usar logs descriptivos
- Evitar logs masivos

### Logger de NestJS

- NestJS tiene su propio sistema de logging.

```ts
import { Logger } from "@nestjs/common";

// Se identifica de qué parte del sistema viene el log:
// Usar el nombre del service, controller o módulo actual
const logger = new Logger("AuthService");

// Equivalente al console.log():
logger.log("Usuario autenticado");

// En errores críticos reales, excepciones o fallos de sistema:
logger.error("Error al autenticar");

// Advertencia, situaciones peligrosas o estados anómalos:
logger.warn("Token próximo a expirar");

// Información técnica para desarrolladores, debug profundo:
// En producción suele estar desactivado
logger.debug("Payload JWT");

// Información ultradetallada con traza completa del sistema:
logger.verbose("Detalles");
```

- En resumen:
  | Nivel | Método Logger | Cuándo usarlo | Ejemplo |
  |-------|-------------|--------------|---------|
  | ✅ Normal | `logger.log()` | Eventos normales del sistema | Login exitoso |
  | ⚠️ Advertencia | `logger.warn()` | Situaciones sospechosas o no críticas | Token próximo a expirar |
  | ❌ Error | `logger.error()` | Errores reales o fallos del sistema | Error al autenticar |
  | 🔍 Debug | `logger.debug()` | Información técnica para depuración | Payload JWT |
  | 🔬 Verbose | `logger.verbose()` | Trazas muy detalladas del sistema | Headers de la request |

📌 Ventajas:

- Integrado con NestJS
- Puede desactivarse por entorno
- Permite contexto

[Volver a Indice](#indice)

---

## Debug paso a paso con breakpoints

### ✅ Debug con VS Code

📌 Configurar launch.json

```json
{
  "version": "0.2.0",
  "configurations": [
    {
      "type": "node",
      "request": "launch",
      "name": "Debug NestJS",
      "runtimeArgs": [
        "-r",
        "ts-node/register",
        "-r",
        "tsconfig-paths/register"
      ],
      "args": ["${workspaceFolder}/src/main.ts"],
      "cwd": "${workspaceFolder}",
      "protocol": "inspector",
      "console": "integratedTerminal",
      "sourceMaps": true,
      "skipFiles": ["<node_internals>/**"]
    }
  ]
}
```

📌 Uso de breakpoints

- Click en la línea de código
- Ejecutar en modo debug
- Inspeccionar variables

[Volver a Indice](#indice)

---

## Debug en capas de NestJS

### ✅ 1) Controllers

```ts
@Get(':id')
  async findOne(@Param('id') id: string) {
  console.log('ID recibido:', id);
  return this.userService.findOne(id);
}
```

### ✅ 2) Services

```ts
async findOne(id: string) {
  const user = await this.repo.findOne({ where: { id } });
  if (!user) {
    throw new NotFoundException('Usuario no encontrado');
  }
  return user;
}
```

### ✅ 3) Providers y DI (Dependency Injection - Inyección de dependencias)

Problemas comunes:

- Provider no registrado
- Scope incorrecto

📌 Tip:

```ts
console.log(this.userService);
```

[Volver a Indice](#indice)

---

## Debug en autenticación JWT

- ✅ Verificar token:

```ts
console.log(req.user);
```

✅ Debug del payload JWT:

```ts
const payload = this.jwtService.decode(token);
console.log(payload);
```

✅ Problemas comunes

- Token vencido
- Secret incorrecto
- Guards mal configurados

[Volver a Indice](#indice)

---

## Debug en Base de Datos

✅ TypeORM / Prisma

```ts
logging: true;
```

✅ Consultas

```ts
console.log(query);
```

✅ Errores típicos

- Conexión fallida
- Entidades mal definidas
- Migraciones incorrectas

[Volver a Indice](#indice)

---

## Debug con variables de entorno

📌 Tip:
- Verificar .env
- Validar ConfigModule
- console.log() de variables de entorno
```ts
console.log(process.env.DB_HOST);
```

[Volver a Indice](#indice)

---

## Manejo de errores y Exception Filters

- @Catch() es un decorador que le dice a NestJS:
  - 👉 “Esta clase va a capturar excepciones (errores)”.
  - Convierte una clase en un Exception Filter.

- Un Exception Filter es un mecanismo de NestJS para:
  - Interceptar errores
  - Manejarlos
  - Transformarlos en respuestas HTTP
  - 👉 Es como un try/catch global del framework.

```ts
@Catch()
export class AllExceptionsFilter implements ExceptionFilter {
  catch(exception: any, host: ArgumentsHost) {
    console.error(exception);
  }
}
```

[Volver a Indice](#indice)

---

## Debug avanzado

✅ Interceptors

```ts
console.log("Interceptor ejecutado");
```

✅ Middleware

```ts
console.log(req.method, req.url);
```

✅ Pipes

```ts
console.log(value);
```

[Volver a Indice](#indice)

---

## Estrategias de debug

### 🧠 Regla 80/20 del debug

El 80% de los errores en NestJS son:

- ❗ await olvidado
- ❗ provider no registrado
- ❗ módulo mal importado
- ❗ DTO mal definido
- ❗ token inválido

### 🔎 Método sistemático

📌 Principios clave

- Reproducir el error
- Aislar el problema
- Inspeccionar variables
- Verificar flujo
- Confirmar hipótesis

📌 Preguntas clave

- ¿Qué esperaba que pase?
- ¿Qué pasó realmente?
- ¿Dónde se rompe el flujo?

### Capas de NestJS y qué debuggear

| Capa        | Qué revisar               |
| ----------- | ------------------------- |
| Controller  | Params, Body, Query       |
| Service     | Lógica de negocio         |
| DTO         | Validaciones              |
| Guard       | Auth / Roles              |
| Interceptor | Transformación            |
| Pipe        | Validación / parseo       |
| Provider    | Inyección de dependencias |
| Module      | Imports / exports         |
| DB          | Query / conexión          |

### Flujo general de Nest JS

```txt
  Request
  ↓
  Middleware
  ↓
  Guard
  ↓
  Pipe
  ↓
  Interceptor (before)
  ↓
  Controller
  ↓
  Service
  ↓↑   |
  Rep  |
  ↓↑   |
  DB   |
      |
      ↓
  Interceptor (after)
      ↓
  Response
```

[Volver a Indice](#indice)

---

## Resumen Debug en NestJS

- El debug en NestJS no consiste solo en imprimir mensajes en consola, sino en entender el flujo completo del framework y saber en qué capa puede estar el problema.
- NestJS está construido sobre una arquitectura modular, donde cada capa tiene una responsabilidad clara. Por eso, cuando algo falla, la clave no es “probar al azar”, sino seguir el recorrido de la request y analizar cada punto del proceso.

### 🧭 Idea central

Toda request en NestJS sigue este camino:
- Request → Middleware → Guard → Pipe → Controller → Service → DB → Response

Debuggear es simplemente responder esta pregunta:
- ¿En qué parte del flujo está fallando?

### 🛠️ Herramientas fundamentales de debug

En NestJS, las principales herramientas son:
- console → debug rápido y simple
- Logger → logging profesional y estructurado
- Breakpoints → análisis paso a paso
- Exception Filters (@Catch) → manejo centralizado de errores
- Logs en DB / JWT / Guards → inspección de capas críticas

### 🧩 Mentalidad correcta de debug

Un buen backend developer no busca el error al azar, sino que sigue un método:
- Reproducir el error
- Aislar la capa donde falla
- Inspeccionar los datos
- Validar hipótesis
- Confirmar la solución

### 👉 Debug no es intuición, es método

La mayoría de los errores en NestJS suelen estar en:
- ❗ Inyección de dependencias (Providers / Modules)
- ❗ Validaciones (DTO / Pipes)
- ❗ async/await mal usado
- ❗ Guards y JWT
- ❗ Configuración de módulos o DB

Si dominás estas áreas, resolvés el 80% de los bugs.

### 🚀 Conclusión final

- Debuggear en NestJS es aprender a pensar como el framework.
- Cuando entendés cómo fluye la request, cómo funciona la DI, cómo interactúan controllers, services y providers, el debug deja de ser frustrante y se convierte en una herramienta poderosa.
- 💡 En ese punto, ya no “probás cosas”, sino que sabés dónde mirar.

[Volver a Indice](#indice)

---

[Volver a Inicio](../../README.md)