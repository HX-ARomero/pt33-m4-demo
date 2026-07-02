# Prompting de Backend

[Volver](../../README.md)

> Un buen prompting para backend no es solo “pedirle algo a la IA”, sino diseñar entradas estructuradas para que el modelo actúe como parte de tu sistema (casi como si fuera otro servicio más). En backend, el prompting se vuelve más riguroso porque afecta lógica, datos y decisiones automatizadas.

## 🧠 1. Qué es prompting en backend

- En frontend podemos improvisar más.
- En backend, un prompt es:
  - Una especificación determinística (o lo más posible) para que un modelo produzca resultados consistentes, seguros y procesables.
  - Se parece más a escribir:
    - Contratos de API (estructuras de datos)
    - Schemas
    - Reglas de negocio

## ⚙️ 2. Qué incluye un buen prompt (estructura ideal)

- Un prompt backend bien hecho suele tener estas partes:
  1. Rol (muy importante):
  - Define cómo debe comportarse el modelo.
  - Por ejemplo: Eres un servicio backend que valida datos de usuarios.
  2. Contexto
  - Información necesaria para tomar decisiones.
  - Por ejemplo: El usuario está registrándose en una app de e-commerce.

3. Tarea específica

- Qué tiene que hacer EXACTAMENTE.
- Por ejemplo: Valida el email y la contraseña según reglas dadas.

4. Reglas / constraints (clave en backend)

- Esto es lo que evita respuestas "creativas".
- Por ejemplo:
  - El email debe tener formato válido
  - La contraseña mínimo 8 caracteres
  - No inventes datos

5. Formato de salida (OBLIGATORIO en backend)

- Sin esto, tu sistema se rompe.
- Por ejemplo:

```ts
{
  "valid": boolean,
  "errors": string[]
}
```

6. Ejemplos (opcional pero potente)

- Reduce ambigüedad.
- Por ejemplo:

```ts
Input: { email: "test", password: "123" }
Output: { "valid": false, "errors": ["email inválido", "password débil"] }
```

## 🧩 3. Tipos de prompting usados en backend

### 🟢 1. Prompting estructurado (el más importante)

- Se usa para APIs.
  - JSON output
  - schemas
  - validación

### 🟢 2. Prompting con instrucciones estrictas

- Para evitar alucinaciones.
- Si no sabes la respuesta, devuelve null.

### 🟢 3. Few-shot prompting

- Damos ejemplos para guiar comportamiento.

### 🟢 4. Chain of Thought (con cuidado)

- No siempre se usa en backend porque:
  - hace outputs más largos
  - menos determinista

### 🟢 5. Tool / function calling

- El modelo decide qué función ejecutar.
- Por ejemplo:
  - `createUser()`
  - `sendEmail()`

## 🧪 4. Buenas prácticas (esto es lo que separa amateurs de pros)

### 🔒 1. Siempre forzar formato

Nunca confíes en texto libre.

✔️ JSON
✔️ enums
✔️ tipos claros

### 🔁 2. Validar la respuesta del modelo

Nunca confíes ciegamente.

if (!response.valid) throw new Error();

### 🧱 3. Hacer prompts reutilizables

Separarlos como si fueran módulos:

/prompts
validateUser.prompt.ts
classifyText.prompt.ts

### 🧼 4. Evitar ambigüedad

Mal prompt:

Analiza este texto

Buen prompt:

Clasifica el texto en: positivo, negativo o neutro

### 🎯 5. Ser específico > ser corto

Más contexto = mejores resultados (hasta cierto punto).

### 🧯 6. Manejo de errores

Siempre contemplar:

respuestas inválidas
JSON roto
timeouts

## 🧰 5. Tecnologías usadas en prompting backend

1.Lenguajes / frameworks

- Node.js (muy común en tu caso)
- NestJS ✅
- Python (FastAPI, Django)

2. Librerías de IA

- LangChain → manejo de prompts y chains
- OpenAI API → acceso a modelos
- LlamaIndex → RAG

3. Validación de datos

- class-validator (NestJS)

4. Bases de datos

- PostgreSQL
- MongoDB
- Otros

5. Técnicas avanzadas

- RAG (Retrieval-Augmented Generation)
  - combinas IA + base de datos
  - reduces alucinaciones
- Caching
  - guardar respuestas del modelo
  - ahorrar costo y latencia

🧩 Prompt templates

- variables dinámicas

```ts
`Analiza el siguiente texto: ${input}`;
```

## ❌ 6. Problemas comunes

1. No definir formato

- → rompe tu API

2. Prompts demasiado vagos

- → respuestas inconsistentes

3. No validar output

- → bugs invisibles

4. Meter lógica en el modelo

- → mala práctica (la lógica debe estar en tu código)

## 🧠 7. Mentalidad correcta

Pensá al modelo como:

- ❌ “No como una IA mágica”
- ✅ “Sino como una función probabilística que necesita especificaciones estrictas”

## 🚀 8. Resumen

Un buen prompt backend:

1. tiene rol claro
2. define tarea específica
3. impone reglas estrictas
4. obliga a un formato estructurado
5. incluye ejemplos si es necesario
6. es validado por código

## 🧪 9. Ejemplo completo: Mini proyecto: AI Ticket Classifier (Backend real)

> A continuación veremos un ejemplo realista, limpio y cercano a producción, usando NestJS + prompts bien diseñados + validación + manejo de errores.

### 🎯 Objetivo del mini proyecto

Construir un endpoint backend que:

- Reciba un texto de un usuario (ticket) y devuelva una clasificación estructurada y validable que pueda ser consumida por otros servicios.

Concretamente, el sistema debe:

1. Clasificar el ticket en:

- soporte, ventas o bug

2. Determinar prioridad:

- baja, media o alta

3. Devolver un JSON estricto, sin texto adicional
4. Ser determinista y robusto (apto para producción)

### 🧠 Prompt completo (nivel producción)

```txt
Eres un servicio backend de clasificación de tickets.

CONTEXTO:
Estás integrado en un sistema backend. Tu respuesta será procesada automáticamente por código.
No eres un asistente conversacional.

TAREA:
Analizar el texto de un ticket y:

1. Clasificarlo en una categoría:
   - soporte
   - ventas
   - bug

2. Determinar su nivel de prioridad:
   - baja
   - media
   - alta

REGLAS:

- Usa exclusivamente las categorías y prioridades definidas
- No inventes información
- No agregues explicaciones
- No incluyas texto fuera del JSON
- Si el texto es ambiguo, elige la opción más probable
- Si el ticket describe un error técnico, clasifícalo como "bug"
- Si el ticket menciona problemas de uso o dudas, clasifícalo como "soporte"
- Si el ticket menciona precios, compras o información comercial, clasifícalo como "ventas"

FORMATO DE RESPUESTA (OBLIGATORIO):
Responde únicamente en JSON válido, sin texto adicional:

{
  "category": "soporte" | "ventas" | "bug",
  "priority": "baja" | "media" | "alta"
}

EJEMPLOS:

Input: "No puedo iniciar sesión en mi cuenta"
Output:
{
  "category": "soporte",
  "priority": "alta"
}

Input: "¿Cuánto cuesta el plan premium?"
Output:
{
  "category": "ventas",
  "priority": "media"
}

Input: "La aplicación se cierra cuando hago clic en guardar"
Output:
{
  "category": "bug",
  "priority": "alta"
}
```

### 🔍 Desglose del prompt:

#### 1. 🧠 Rol

- Eres un servicio backend de clasificación de tickets.
  - 👉 Esto elimina comportamiento “chatty”
  - 👉 Hace que el modelo piense como sistema, no como asistente

#### 2. 🌐 Contexto

- Estás integrado en un sistema backend...
- No eres un asistente conversacional.
- 👉 Clave para:
  - evitar explicaciones
  - evitar texto extra
  - mejorar consistencia

#### 3. 🎯 Tarea

- 👉 Define exactamente qué debe hacer
- 👉 Evita respuestas abiertas

```txt
Analizar el texto...
1. Clasificar...
2. Determinar prioridad...
```

#### 4. 📏 Reglas (la parte más importante)

- Usa exclusivamente...
- No inventes información
- No agregues explicaciones

- 👉 Esto reduce:
  - alucinaciones
  - texto basura
  - errores de formato

💡 Nota clave:
Estas reglas son lo que convierte un prompt “normal” en uno de backend.

#### 5. 🧠 Reglas de negocio

- Si describe error técnico → bug
- Si dudas → soporte
- Si compras → ventas

👉 Esto reemplaza lógica difusa
👉 Hace el sistema más consistente

#### 6. 📦 Formato de salida

- 👉 Es lo MÁS crítico en backend
  - Reduce ambigüedad
  - Mejora precisión en edge cases
  - Hace el comportamiento más estable
- Sin esto:
  - no podés parsear
  - no podés validar
  - rompés el sistema
- Por ejemplo:

```ts
{
  "category": "...",
  "priority": "..."
}
```

#### 7. Este prompt ya cumple con:

✔ determinismo razonable
✔ output estructurado
✔ reglas de negocio claras
✔ control de alucinaciones
✔ facilidad de validación (class-validator)

[Volver](../../README.md)