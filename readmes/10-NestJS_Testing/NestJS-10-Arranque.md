# 🚀 Secuencia de arranque en NestJS

[Volver a Inicio](../../README.md)

## 1️⃣ main.ts — Punto de entrada

- Se crea la aplicación
- Se pasa el AppModule como módulo raíz

```ts
async function bootstrap() {
  const app = await NestFactory.create(AppModule);
  await app.listen(3000);
}
bootstrap();
```

## 2️⃣ NestFactory.create(AppModule)

Aquí comienza el proceso interno:

- Se crea el contenedor de dependencias (IoC Container)
  - Nest analiza:
    - @Module()
    - providers
    - controllers
    - imports
    - exports

## 3️⃣ Resolución de módulos (Dependency Graph)

Nest crea el Dependency Injection Container:

- Lee AppModule
- Carga módulos importados
- Resuelve dependencias
- Construye el árbol completo de inyección

## 4️⃣ Instanciación de Providers

Se crean:

- Services
- Repositories
- Guards
- Pipes
- Interceptors

## 5️⃣ Instanciación de Controllers

Una vez que los servicios existen, Nest crea los controllers e inyecta dependencias.

## 6️⃣ Registro del sistema HTTP

Por defecto:

- Usa Express (Puede usar Fastify si se configura)
- Aquí se:
  - Registran rutas
  - Vinculan controllers con endpoints
  - Configuran middlewares globales

## 7️⃣ Aplicación de configuración global

Se registran ahora pipes, guards interceptors y filters si se declararon en main.ts:

- app.useGlobalPipes(...)
- app.useGlobalGuards(...)
- app.useGlobalInterceptors(...)
- app.useGlobalFilters(...)

## 8️⃣ app.listen()

Aquí:

- Se abre el puerto
- Se inicia el servidor HTTP
- La app queda escuchando requests

<div style="text-align: center;">
  <img src="./assets/10-NestJS.png" style="width: 700px;" alt="Tipos de Testing">
</div>

## 🧠 Qué es lo más importante entender

Nest es:

- Modular
- Basado en Dependency Injection
- Orientado a metadata (decoradores)
- Basado en un contenedor IoC
- Primero construye estructura interna
- Después arranca el servidor

---

[Volver a Inicio](../../README.md)
