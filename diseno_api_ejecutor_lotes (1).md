# Diseño de API - Ejecutor de Lotes

## 1. Introducción

Este documento presenta el diseño de la API para la práctica **Ejecutor de Lotes**. El sistema simula un mecanismo de ejecución por lotes en el que primero se registran programas y ficheros, y posteriormente se ejecutan procesos usando los identificadores generados por dichos registros.

En esta práctica, la API no se entiende como una API web basada en HTTP, sino como un **contrato de comunicación entre procesos**. Los procesos se comunican mediante **tuberías nombradas** y los mensajes se envían en formato **JSON**.

El diseño define:

- La estructura de los mensajes JSON.
- Las operaciones disponibles para programas, ficheros y lotes.
- Las respuestas esperadas para cada operación.
- Los errores posibles en cada operación.
- La forma en que `ctrllt` enruta las peticiones.
- Las decisiones necesarias para una implementación posterior en Linux y Windows.

---

## 2. Componentes del sistema

El sistema está compuesto por los siguientes componentes:

| Componente | Función |
|---|---|
| `cliente` | Proceso que construye solicitudes en formato JSON y las envía a `ctrllt` mediante tuberías nombradas. También recibe las respuestas asociadas a sus peticiones. |
| `ctrllt` | Componente central de control. Recibe las peticiones del cliente, valida la estructura del mensaje, identifica el recurso solicitado y redirige la petición hacia `gesprog`, `gesfich` o `ejecutor`. |
| `gesprog` | Servicio de gestión de programas. Registra programas ejecutables en `aralmac`, consulta programas existentes, actualiza su información, los elimina y atiende operaciones de control del servicio. |
| `gesfich` | Servicio de gestión de ficheros. Crea ficheros en `aralmac`, consulta su contenido o listado, actualiza su contenido desde una ruta externa, los elimina y atiende operaciones de control del servicio. |
| `ejecutor` | Servicio de ejecución de lotes. Recibe solicitudes de ejecución, conecta ficheros y programas registrados, consulta estados de lotes, mata o detiene lotes en ejecución y atiende operaciones de control del ejecutor. |
| `aralmac` | Área de almacenamiento utilizada por los servicios para guardar programas, ficheros y metadatos necesarios para la ejecución de lotes. |

Arquitectura general:

```text
cliente
   |
   v
ctrllt
   |----------------> gesprog
   |----------------> gesfich
   |----------------> ejecutor
                         |
                         v
                      aralmac
```

El cliente no se comunica directamente con `gesprog`, `gesfich` o `ejecutor`. El cliente envía todas sus peticiones a `ctrllt`; luego `ctrllt` revisa el campo `recurso` del JSON y decide a qué servicio debe reenviar la petición.

---

## 3. Modelo de comunicación

### 3.1. Tuberías nombradas

La comunicación entre los procesos principales del sistema se realizará mediante tuberías nombradas.

Para este diseño se adopta un modelo **half-duplex**, en el que se utiliza una tubería para enviar peticiones y otra tubería para recibir respuestas. Este modelo permite mantener una separación explícita entre los canales de solicitud y respuesta, y es compatible con sistemas donde las tuberías nombradas son unidireccionales.

### 3.2. Separación entre transporte y mensaje

El diseño separa la capa de transporte de la estructura lógica del mensaje. Las tuberías nombradas se definen como canales de comunicación entre procesos, mientras que los mensajes JSON contienen únicamente la información necesaria para procesar una solicitud o respuesta.

Por esta razón, los nombres de las tuberías no se incluyen dentro del cuerpo JSON. Estos nombres se definen al iniciar los procesos o al configurar la comunicación entre componentes.

### 3.3. Tuberías propuestas

| Comunicación | Petición | Respuesta |
|---|---|---|
| Cliente ↔ `ctrllt` | `cliente_to_ctrllt` | `ctrllt_to_cliente` |
| `ctrllt` ↔ `gesprog` | `ctrllt_to_gesprog` | `gesprog_to_ctrllt` |
| `ctrllt` ↔ `gesfich` | `ctrllt_to_gesfich` | `gesfich_to_ctrllt` |
| `ctrllt` ↔ `ejecutor` | `ctrllt_to_ejecutor` | `ejecutor_to_ctrllt` |

Estas tuberías se crean al iniciar los servicios y se reutilizan para múltiples peticiones durante la ejecución del sistema.

### 3.4. Nombres sugeridos en Linux

| Tubería | Dirección |
|---|---|
| `/tmp/lotes-ctrllt-req` | Cliente → `ctrllt` |
| `/tmp/lotes-ctrllt-res` | `ctrllt` → Cliente |
| `/tmp/lotes-gesprog-req` | `ctrllt` → `gesprog` |
| `/tmp/lotes-gesprog-res` | `gesprog` → `ctrllt` |
| `/tmp/lotes-gesfich-req` | `ctrllt` → `gesfich` |
| `/tmp/lotes-gesfich-res` | `gesfich` → `ctrllt` |
| `/tmp/lotes-ejecutor-req` | `ctrllt` → `ejecutor` |
| `/tmp/lotes-ejecutor-res` | `ejecutor` → `ctrllt` |

### 3.5. Nombres sugeridos en Windows

| Tubería | Dirección |
|---|---|
| `\\.\pipe\lotes-ctrllt-req` | Cliente → `ctrllt` |
| `\\.\pipe\lotes-ctrllt-res` | `ctrllt` → Cliente |
| `\\.\pipe\lotes-gesprog-req` | `ctrllt` → `gesprog` |
| `\\.\pipe\lotes-gesprog-res` | `gesprog` → `ctrllt` |
| `\\.\pipe\lotes-gesfich-req` | `ctrllt` → `gesfich` |
| `\\.\pipe\lotes-gesfich-res` | `gesfich` → `ctrllt` |
| `\\.\pipe\lotes-ejecutor-req` | `ctrllt` → `ejecutor` |
| `\\.\pipe\lotes-ejecutor-res` | `ejecutor` → `ctrllt` |

### 3.6. Delimitación de mensajes

Cada mensaje JSON se enviará codificado en UTF-8 y terminado con salto de línea:

```text
\n
```

Esto permite que el receptor identifique el final de cada mensaje.

---

## 4. Compatibilidad Linux y Windows

Como el trabajo será desarrollado en pareja, el sistema debe poder ejecutarse tanto en Linux como en Windows 11.

La API JSON será igual en ambos sistemas operativos. La diferencia estará únicamente en la capa de implementación de bajo nivel.

| Elemento | Linux | Windows |
|---|---|---|
| Tuberías nombradas | FIFO / POSIX named pipes | Windows Named Pipes |
| Creación de procesos | `fork`, `exec`, `dup2`, `waitpid`, `kill` | `CreateProcess`, `WaitForSingleObject`, `TerminateProcess` |
| Formato de mensajes | JSON | JSON |
| Servicios | Los mismos | Los mismos |
| API | La misma | La misma |

Para una implementación posterior se recomienda mantener una lógica común y separar las funciones dependientes del sistema operativo.

Ejemplo de separación futura:

```text
src/
├── protocolo/
├── servicios/
├── almacenamiento/
├── ipc/
│   ├── pipe_linux.cpp
│   └── pipe_windows.cpp
└── procesos/
    ├── proceso_linux.cpp
    └── proceso_windows.cpp
```

---

## 5. Recursos principales de la API

La API manejará tres recursos principales:

| Recurso | Servicio destino | Descripción |
|---|---|---|
| `programa` | `gesprog` | Operaciones sobre programas registrados. |
| `fichero` | `gesfich` | Operaciones sobre ficheros registrados. |
| `lote` | `ejecutor` | Operaciones de ejecución y control de procesos de lote. |

Las operaciones de control se asocian al recurso que administra cada servicio. Por tanto, las operaciones `suspender`, `reasumir` y `terminar` aplican a los recursos `programa` y `fichero`, mientras que `suspender`, `reasumir` y `parar` aplican al recurso `lote`.

---

## 6. Formato estándar de mensajes JSON

Todos los mensajes del sistema siguen una estructura estándar. Las peticiones conservan los mismos campos base para todos los recursos, y las respuestas mantienen una estructura común tanto en casos exitosos como en casos de error. Los campos `datos`, `resultado` y `error` contienen la información específica de cada operación.

---

### 6.1. Formato estándar de petición

Toda petición tendrá la siguiente estructura:

```json
{
  "tipo_mensaje": "peticion",
  "id_cliente": "cli-0001",
  "id_peticion": "req-0001",
  "recurso": "programa",
  "operacion": "guardar",
  "datos": {}
}
```

| Campo | Tipo | Obligatorio | Descripción |
|---|---|---:|---|
| `tipo_mensaje` | cadena | Sí | Siempre será `peticion`. |
| `id_cliente` | cadena | Sí | Identifica al cliente que envía la petición. |
| `id_peticion` | cadena | Sí | Identifica la petición enviada por ese cliente. |
| `recurso` | cadena | Sí | Puede ser `programa`, `fichero` o `lote`. |
| `operacion` | cadena | Sí | Operación solicitada sobre el recurso. |
| `datos` | objeto | Sí | Datos necesarios para ejecutar la operación. Si la operación no necesita datos, se envía `{}`. |

---

### 6.2. Formato estándar de respuesta exitosa

Toda respuesta exitosa tendrá la siguiente estructura:

```json
{
  "tipo_mensaje": "respuesta",
  "id_cliente": "cli-0001",
  "id_peticion": "req-0001",
  "estado": "ok",
  "resultado": {},
  "error": null,
  "mensaje": "Operación realizada correctamente"
}
```

| Campo | Tipo | Descripción |
|---|---|---|
| `tipo_mensaje` | cadena | Siempre será `respuesta`. |
| `id_cliente` | cadena | Mismo identificador recibido en la petición. |
| `id_peticion` | cadena | Mismo identificador recibido en la petición. |
| `estado` | cadena | `ok` cuando la operación fue exitosa. |
| `resultado` | objeto | Información producida por la operación. |
| `error` | nulo | En respuestas exitosas siempre será `null`. |
| `mensaje` | cadena | Mensaje corto y entendible para el cliente. |

---

### 6.3. Formato estándar de respuesta con error

Toda respuesta con error tendrá la siguiente estructura:

```json
{
  "tipo_mensaje": "respuesta",
  "id_cliente": "cli-0001",
  "id_peticion": "req-0001",
  "estado": "error",
  "resultado": {},
  "error": {
    "codigo": "PROGRAMA_NO_EXISTE",
    "detalle": "No existe un programa registrado con id p-9999"
  },
  "mensaje": "No fue posible completar la operación"
}
```

| Campo | Tipo | Descripción |
|---|---|---|
| `tipo_mensaje` | cadena | Siempre será `respuesta`. |
| `id_cliente` | cadena | Mismo identificador recibido en la petición. |
| `id_peticion` | cadena | Mismo identificador recibido en la petición. |
| `estado` | cadena | `error` cuando la operación no pudo completarse. |
| `resultado` | objeto | En errores se envía `{}`. |
| `error` | objeto | Contiene `codigo` y `detalle`. |
| `mensaje` | cadena | Mensaje corto explicando que la operación falló. |

Regla general:

```text
Si estado = ok:
    resultado contiene la información útil.
    error = null.

Si estado = error:
    resultado = {}.
    error contiene codigo y detalle.
```

---

## 7. Identificadores del sistema

| Identificador | Formato | Lo genera | Descripción |
|---|---|---|---|
| `id_cliente` | `cli-XXXX` | Cliente | Identifica al cliente. |
| `id_peticion` | `req-XXXX` | Cliente | Identifica cada petición enviada por un cliente. |
| `id_programa` | `p-XXXX` | `gesprog` | Identifica un programa registrado. |
| `id_fichero` | `f-XXXX` | `gesfich` | Identifica un fichero registrado. |
| `id_lote` | `l-XXXX` | `ejecutor` | Identifica una ejecución de lote. |

Ejemplos:

```text
cli-0001
req-0001
p-0001
f-0001
l-0001
```

---

## 8. Enrutamiento en `ctrllt`

`ctrllt` recibe todas las peticiones del cliente y las redirige según el campo `recurso`.

| `recurso` recibido | Servicio destino |
|---|---|
| `programa` | `gesprog` |
| `fichero` | `gesfich` |
| `lote` | `ejecutor` |

Ejemplo:

```json
{
  "tipo_mensaje": "peticion",
  "id_cliente": "cli-0001",
  "id_peticion": "req-0001",
  "recurso": "programa",
  "operacion": "guardar",
  "datos": {
    "ejecutable": "/home/isa/programas/actividad.py",
    "argumentos": ["sumar"],
    "ambiente": {}
  }
}
```

Como `recurso` es `programa`, `ctrllt` reenvía la petición a `gesprog`.

`ctrllt` no guarda programas, no crea ficheros y no ejecuta lotes. Su función principal es recibir, analizar, redirigir y devolver la respuesta al cliente.

---

## 9. API de `gesprog` - Recurso `programa`

`gesprog` administra los programas registrados en `aralmac`.

Un programa registrado contiene:

- `id_programa`
- ejecutable
- argumentos
- ambiente
- descripción opcional

Los argumentos representan opciones o parámetros pequeños de ejecución. Los datos principales que el programa procesará deben entregarse mediante ficheros.

Ejemplo:

```text
fichero de entrada: 10, 20, 30
argumento del programa: sumar
programa: actividad.py
```

---

### 9.1. Operación `guardar`

Registra un nuevo programa ejecutable.

#### Petición

```json
{
  "tipo_mensaje": "peticion",
  "id_cliente": "cli-0001",
  "id_peticion": "req-0001",
  "recurso": "programa",
  "operacion": "guardar",
  "datos": {
    "ejecutable": "/home/isa/programas/actividad.py",
    "argumentos": ["sumar"],
    "ambiente": {
      "PYTHONUNBUFFERED": "1"
    },
    "descripcion": "Programa que suma números recibidos por entrada estándar"
  }
}
```

#### Datos requeridos

| Campo en `datos` | Tipo | Obligatorio | Descripción |
|---|---|---:|---|
| `ejecutable` | cadena | Sí | Ruta o nombre del ejecutable válido. Puede ser binario o script. |
| `argumentos` | arreglo | Sí | Lista de argumentos con los que se ejecutará el programa. Puede ser `[]`. |
| `ambiente` | objeto | Sí | Variables de ambiente para el programa. Puede ser `{}`. |
| `descripcion` | cadena | No | Descripción del programa. |

#### Respuesta exitosa

```json
{
  "tipo_mensaje": "respuesta",
  "id_cliente": "cli-0001",
  "id_peticion": "req-0001",
  "estado": "ok",
  "resultado": {
    "id_programa": "p-0001"
  },
  "error": null,
  "mensaje": "Programa registrado correctamente"
}
```

#### Errores posibles

| Código | Cuándo ocurre |
|---|---|
| `CAMPO_OBLIGATORIO_FALTANTE` | Falta `ejecutable`, `argumentos` o `ambiente`. |
| `EJECUTABLE_NO_EXISTE` | La ruta del ejecutable no existe. |
| `EJECUTABLE_INVALIDO` | El archivo no puede ejecutarse o no es un ejecutable válido. |
| `SERVICIO_SUSPENDIDO` | `gesprog` está suspendido. |
| `ERROR_ALMACENAMIENTO` | No se pudo copiar o registrar el programa en `aralmac`. |

---

### 9.2. Operación `leer`

Permite consultar un programa específico o listar todos los programas.

#### Leer programa específico

```json
{
  "tipo_mensaje": "peticion",
  "id_cliente": "cli-0001",
  "id_peticion": "req-0002",
  "recurso": "programa",
  "operacion": "leer",
  "datos": {
    "id_programa": "p-0001"
  }
}
```

#### Respuesta exitosa para programa específico

```json
{
  "tipo_mensaje": "respuesta",
  "id_cliente": "cli-0001",
  "id_peticion": "req-0002",
  "estado": "ok",
  "resultado": {
    "id_programa": "p-0001",
    "ejecutable": "actividad.py",
    "argumentos": ["sumar"],
    "ambiente": {
      "PYTHONUNBUFFERED": "1"
    },
    "descripcion": "Programa que suma números recibidos por entrada estándar"
  },
  "error": null,
  "mensaje": "Programa encontrado"
}
```

#### Leer todos los programas

```json
{
  "tipo_mensaje": "peticion",
  "id_cliente": "cli-0001",
  "id_peticion": "req-0003",
  "recurso": "programa",
  "operacion": "leer",
  "datos": {}
}
```

#### Respuesta exitosa para todos los programas

```json
{
  "tipo_mensaje": "respuesta",
  "id_cliente": "cli-0001",
  "id_peticion": "req-0003",
  "estado": "ok",
  "resultado": {
    "programas": [
      {
        "id_programa": "p-0001",
        "ejecutable": "actividad.py",
        "argumentos": ["sumar"],
        "descripcion": "Programa que suma números"
      },
      {
        "id_programa": "p-0002",
        "ejecutable": "dividir.py",
        "argumentos": ["2"],
        "descripcion": "Programa que divide entre 2"
      }
    ]
  },
  "error": null,
  "mensaje": "Listado de programas registrados"
}
```

#### Errores posibles

| Código | Cuándo ocurre |
|---|---|
| `PROGRAMA_NO_EXISTE` | Se envía un `id_programa` que no está registrado. |
| `SERVICIO_SUSPENDIDO` | `gesprog` está suspendido y no acepta la operación en ese estado. |
| `ERROR_ALMACENAMIENTO` | No se pudo acceder a la información almacenada. |

---

### 9.3. Operación `actualizar`

Actualiza la información de un programa ya registrado.

#### Petición

```json
{
  "tipo_mensaje": "peticion",
  "id_cliente": "cli-0001",
  "id_peticion": "req-0004",
  "recurso": "programa",
  "operacion": "actualizar",
  "datos": {
    "id_programa": "p-0001",
    "ejecutable": "/home/isa/programas/actividad_v2.py",
    "argumentos": ["promediar"],
    "ambiente": {},
    "descripcion": "Nueva versión del programa"
  }
}
```

#### Datos requeridos

| Campo en `datos` | Tipo | Obligatorio | Descripción |
|---|---|---:|---|
| `id_programa` | cadena | Sí | Programa que se desea actualizar. |
| `ejecutable` | cadena | Sí | Nuevo ejecutable o nueva ruta. |
| `argumentos` | arreglo | Sí | Nueva lista de argumentos. |
| `ambiente` | objeto | Sí | Nuevas variables de ambiente. |
| `descripcion` | cadena | No | Nueva descripción. |

#### Respuesta exitosa

```json
{
  "tipo_mensaje": "respuesta",
  "id_cliente": "cli-0001",
  "id_peticion": "req-0004",
  "estado": "ok",
  "resultado": {
    "id_programa": "p-0001"
  },
  "error": null,
  "mensaje": "Programa actualizado correctamente"
}
```

#### Errores posibles

| Código | Cuándo ocurre |
|---|---|
| `PROGRAMA_NO_EXISTE` | No existe el `id_programa`. |
| `EJECUTABLE_NO_EXISTE` | La nueva ruta del ejecutable no existe. |
| `EJECUTABLE_INVALIDO` | El nuevo ejecutable no es válido. |
| `CAMPO_OBLIGATORIO_FALTANTE` | Falta algún campo requerido. |
| `SERVICIO_SUSPENDIDO` | `gesprog` está suspendido. |
| `ERROR_ALMACENAMIENTO` | No se pudo actualizar la información en `aralmac`. |

---

### 9.4. Operación `borrar`

Elimina un programa registrado.

#### Petición

```json
{
  "tipo_mensaje": "peticion",
  "id_cliente": "cli-0001",
  "id_peticion": "req-0005",
  "recurso": "programa",
  "operacion": "borrar",
  "datos": {
    "id_programa": "p-0001"
  }
}
```

#### Respuesta exitosa

```json
{
  "tipo_mensaje": "respuesta",
  "id_cliente": "cli-0001",
  "id_peticion": "req-0005",
  "estado": "ok",
  "resultado": {
    "id_programa": "p-0001"
  },
  "error": null,
  "mensaje": "Programa borrado correctamente"
}
```

#### Errores posibles

| Código | Cuándo ocurre |
|---|---|
| `PROGRAMA_NO_EXISTE` | No existe el `id_programa`. |
| `PROGRAMA_EN_USO` | El programa está siendo usado por un lote activo. |
| `SERVICIO_SUSPENDIDO` | `gesprog` está suspendido. |
| `ERROR_ALMACENAMIENTO` | No se pudo borrar el programa de `aralmac`. |

---

### 9.5. Operaciones `suspender`, `reasumir` y `terminar`

Estas operaciones controlan el estado del servicio `gesprog`.

#### Petición para suspender

```json
{
  "tipo_mensaje": "peticion",
  "id_cliente": "cli-0001",
  "id_peticion": "req-0006",
  "recurso": "programa",
  "operacion": "suspender",
  "datos": {}
}
```

Para `reasumir` y `terminar` se usa la misma estructura, cambiando el valor de `operacion`.

#### Respuesta exitosa

```json
{
  "tipo_mensaje": "respuesta",
  "id_cliente": "cli-0001",
  "id_peticion": "req-0006",
  "estado": "ok",
  "resultado": {
    "estado_servicio": "suspendido"
  },
  "error": null,
  "mensaje": "Servicio gesprog suspendido correctamente"
}
```

#### Errores posibles

| Código | Cuándo ocurre |
|---|---|
| `OPERACION_INVALIDA` | La operación no puede ejecutarse desde el estado actual. |
| `SERVICIO_TERMINADO` | El servicio ya se encuentra terminado. |

---

## 10. API de `gesfich` - Recurso `fichero`

`gesfich` administra los ficheros almacenados en `aralmac`.

Un fichero puede funcionar como:

- Fuente o entrada de un proceso de lote.
- Destino o salida de un proceso de lote.

El enunciado de la práctica no restringe explícitamente la extensión o tipo de los ficheros administrados por `gesfich`. Para esta primera versión del diseño, los ficheros se tratarán como archivos de texto plano codificados en UTF-8, por ejemplo `.txt`, `.csv`, `.log`, `.dat` o `.json`.

Esta decisión mantiene la API alineada con el modelo de ejecución mediante entrada estándar y salida estándar. El soporte para archivos binarios queda fuera del alcance de esta primera definición.

---

### 10.1. Operación `crear`

Crea un fichero vacío en `aralmac` y devuelve un identificador `f-XXXX`.

#### Petición

```json
{
  "tipo_mensaje": "peticion",
  "id_cliente": "cli-0001",
  "id_peticion": "req-0007",
  "recurso": "fichero",
  "operacion": "crear",
  "datos": {
    "nombre_logico": "numeros_entrada"
  }
}
```

#### Datos requeridos

| Campo en `datos` | Tipo | Obligatorio | Descripción |
|---|---|---:|---|
| `nombre_logico` | cadena | No | Nombre descriptivo para identificar el propósito del fichero. |

El fichero creado queda vacío. Para agregar contenido se debe usar la operación `actualizar`.

#### Respuesta exitosa

```json
{
  "tipo_mensaje": "respuesta",
  "id_cliente": "cli-0001",
  "id_peticion": "req-0007",
  "estado": "ok",
  "resultado": {
    "id_fichero": "f-0001"
  },
  "error": null,
  "mensaje": "Fichero creado correctamente"
}
```

#### Errores posibles

| Código | Cuándo ocurre |
|---|---|
| `SERVICIO_SUSPENDIDO` | `gesfich` está suspendido. |
| `ERROR_ALMACENAMIENTO` | No se pudo crear el fichero en `aralmac`. |

---

### 10.2. Operación `actualizar`

Copia el contenido de un archivo externo dentro de un fichero registrado en `aralmac`.

#### Petición

```json
{
  "tipo_mensaje": "peticion",
  "id_cliente": "cli-0001",
  "id_peticion": "req-0008",
  "recurso": "fichero",
  "operacion": "actualizar",
  "datos": {
    "id_fichero": "f-0001",
    "ruta_origen": "/home/isa/datos/numeros.txt"
  }
}
```

#### Datos requeridos

| Campo en `datos` | Tipo | Obligatorio | Descripción |
|---|---|---:|---|
| `id_fichero` | cadena | Sí | Fichero registrado que será actualizado. |
| `ruta_origen` | cadena | Sí | Ruta del archivo externo cuyo contenido será copiado. |

#### Respuesta exitosa

```json
{
  "tipo_mensaje": "respuesta",
  "id_cliente": "cli-0001",
  "id_peticion": "req-0008",
  "estado": "ok",
  "resultado": {
    "id_fichero": "f-0001"
  },
  "error": null,
  "mensaje": "Fichero actualizado correctamente"
}
```

#### Errores posibles

| Código | Cuándo ocurre |
|---|---|
| `FICHERO_NO_EXISTE` | No existe el `id_fichero`. |
| `RUTA_ORIGEN_NO_EXISTE` | No existe la ruta indicada en `ruta_origen`. |
| `SERVICIO_SUSPENDIDO` | `gesfich` está suspendido. |
| `ERROR_ALMACENAMIENTO` | No se pudo copiar el contenido hacia `aralmac`. |

---

### 10.3. Operación `leer`

Permite leer un fichero específico o listar todos los ficheros registrados.

#### Leer fichero específico

```json
{
  "tipo_mensaje": "peticion",
  "id_cliente": "cli-0001",
  "id_peticion": "req-0009",
  "recurso": "fichero",
  "operacion": "leer",
  "datos": {
    "id_fichero": "f-0001"
  }
}
```

#### Respuesta exitosa para fichero específico

```json
{
  "tipo_mensaje": "respuesta",
  "id_cliente": "cli-0001",
  "id_peticion": "req-0009",
  "estado": "ok",
  "resultado": {
    "id_fichero": "f-0001",
    "contenido": "10\n20\n30\n"
  },
  "error": null,
  "mensaje": "Fichero leído correctamente"
}
```

#### Leer todos los ficheros

```json
{
  "tipo_mensaje": "peticion",
  "id_cliente": "cli-0001",
  "id_peticion": "req-0010",
  "recurso": "fichero",
  "operacion": "leer",
  "datos": {}
}
```

#### Respuesta exitosa para todos los ficheros

```json
{
  "tipo_mensaje": "respuesta",
  "id_cliente": "cli-0001",
  "id_peticion": "req-0010",
  "estado": "ok",
  "resultado": {
    "ficheros": [
      {
        "id_fichero": "f-0001",
        "nombre_logico": "numeros_entrada"
      },
      {
        "id_fichero": "f-0002",
        "nombre_logico": "resultado_final"
      }
    ]
  },
  "error": null,
  "mensaje": "Listado de ficheros registrados"
}
```

#### Errores posibles

| Código | Cuándo ocurre |
|---|---|
| `FICHERO_NO_EXISTE` | Se envía un `id_fichero` que no está registrado. |
| `SERVICIO_SUSPENDIDO` | `gesfich` está suspendido y no acepta la operación en ese estado. |
| `ERROR_ALMACENAMIENTO` | No se pudo acceder al contenido del fichero. |

---

### 10.4. Operación `borrar`

Elimina un fichero registrado.

#### Petición

```json
{
  "tipo_mensaje": "peticion",
  "id_cliente": "cli-0001",
  "id_peticion": "req-0011",
  "recurso": "fichero",
  "operacion": "borrar",
  "datos": {
    "id_fichero": "f-0001"
  }
}
```

#### Respuesta exitosa

```json
{
  "tipo_mensaje": "respuesta",
  "id_cliente": "cli-0001",
  "id_peticion": "req-0011",
  "estado": "ok",
  "resultado": {
    "id_fichero": "f-0001"
  },
  "error": null,
  "mensaje": "Fichero borrado correctamente"
}
```

#### Errores posibles

| Código | Cuándo ocurre |
|---|---|
| `FICHERO_NO_EXISTE` | No existe el `id_fichero`. |
| `FICHERO_EN_USO` | El fichero está siendo usado por un lote activo. |
| `SERVICIO_SUSPENDIDO` | `gesfich` está suspendido. |
| `ERROR_ALMACENAMIENTO` | No se pudo borrar el fichero de `aralmac`. |

---

### 10.5. Operaciones `suspender`, `reasumir` y `terminar`

Estas operaciones controlan el estado del servicio `gesfich`.

#### Petición para suspender

```json
{
  "tipo_mensaje": "peticion",
  "id_cliente": "cli-0001",
  "id_peticion": "req-0012",
  "recurso": "fichero",
  "operacion": "suspender",
  "datos": {}
}
```

Para `reasumir` y `terminar` se usa la misma estructura, cambiando el valor de `operacion`.

#### Respuesta exitosa

```json
{
  "tipo_mensaje": "respuesta",
  "id_cliente": "cli-0001",
  "id_peticion": "req-0012",
  "estado": "ok",
  "resultado": {
    "estado_servicio": "suspendido"
  },
  "error": null,
  "mensaje": "Servicio gesfich suspendido correctamente"
}
```

#### Errores posibles

| Código | Cuándo ocurre |
|---|---|
| `OPERACION_INVALIDA` | La operación no puede ejecutarse desde el estado actual. |
| `SERVICIO_TERMINADO` | El servicio ya se encuentra terminado. |

---

## 11. API de `ejecutor` - Recurso `lote`

`ejecutor` se encarga de ejecutar procesos de lote usando programas y ficheros registrados en `aralmac`.

El cliente indica:

- Qué fichero será la entrada.
- Qué programa o programas se ejecutarán.
- En qué fichero se guardará la salida final.

El ejecutor conecta:

```text
fichero de entrada → stdin del primer programa
stdout de cada programa → stdin del siguiente programa
stdout del último programa → fichero de salida
```

Ejemplo:

```text
f-0001 → p-0001 → p-0002 → f-0002
```

Durante la ejecución del lote, la comunicación entre programas se realiza mediante entrada estándar y salida estándar. Los mensajes JSON se utilizan únicamente para controlar la solicitud de ejecución, no para transportar datos intermedios entre programas.

---

### 11.1. Operación `ejecutar`

Inicia una ejecución de lote.

#### Petición con varios programas

```json
{
  "tipo_mensaje": "peticion",
  "id_cliente": "cli-0001",
  "id_peticion": "req-0013",
  "recurso": "lote",
  "operacion": "ejecutar",
  "datos": {
    "entrada": "f-0001",
    "programas": ["p-0001", "p-0002"],
    "salida": "f-0002"
  }
}
```

#### Datos requeridos

| Campo en `datos` | Tipo | Obligatorio | Descripción |
|---|---|---:|---|
| `entrada` | cadena | Sí | Identificador del fichero que será entrada del primer programa. |
| `programas` | arreglo | Sí | Lista ordenada de programas que se ejecutarán. |
| `salida` | cadena | Sí | Identificador del fichero donde se guardará la salida final. |

#### Interpretación

```text
entrada: f-0001
programas: p-0001, p-0002
salida: f-0002

Flujo:
f-0001 → p-0001 → p-0002 → f-0002
```

#### Respuesta exitosa

```json
{
  "tipo_mensaje": "respuesta",
  "id_cliente": "cli-0001",
  "id_peticion": "req-0013",
  "estado": "ok",
  "resultado": {
    "id_lote": "l-0001",
    "estado_lote": "corriendo"
  },
  "error": null,
  "mensaje": "Lote iniciado correctamente"
}
```

#### Petición con un solo programa

```json
{
  "tipo_mensaje": "peticion",
  "id_cliente": "cli-0001",
  "id_peticion": "req-0014",
  "recurso": "lote",
  "operacion": "ejecutar",
  "datos": {
    "entrada": "f-0001",
    "programas": ["p-0001"],
    "salida": "f-0002"
  }
}
```

Interpretación:

```text
f-0001 → p-0001 → f-0002
```

#### Errores posibles

| Código | Cuándo ocurre |
|---|---|
| `FICHERO_NO_EXISTE` | No existe el fichero de entrada o de salida. |
| `PROGRAMA_NO_EXISTE` | Algún programa del arreglo no existe. |
| `LISTA_PROGRAMAS_VACIA` | El arreglo `programas` está vacío. |
| `SERVICIO_SUSPENDIDO` | El ejecutor está suspendido y no acepta nuevas ejecuciones. |
| `ERROR_EJECUCION_LOTE` | No se pudo crear o ejecutar el proceso de lote. |

---

### 11.2. Operación `estado`

Permite consultar el estado de un lote específico o listar todos los lotes.

#### Consultar lote específico

```json
{
  "tipo_mensaje": "peticion",
  "id_cliente": "cli-0001",
  "id_peticion": "req-0015",
  "recurso": "lote",
  "operacion": "estado",
  "datos": {
    "id_lote": "l-0001"
  }
}
```

#### Respuesta exitosa para lote específico

```json
{
  "tipo_mensaje": "respuesta",
  "id_cliente": "cli-0001",
  "id_peticion": "req-0015",
  "estado": "ok",
  "resultado": {
    "id_lote": "l-0001",
    "estado_lote": "terminado_ok",
    "codigo_salida": 0
  },
  "error": null,
  "mensaje": "Estado del lote consultado correctamente"
}
```

#### Consultar todos los lotes

```json
{
  "tipo_mensaje": "peticion",
  "id_cliente": "cli-0001",
  "id_peticion": "req-0016",
  "recurso": "lote",
  "operacion": "estado",
  "datos": {}
}
```

#### Respuesta exitosa para todos los lotes

```json
{
  "tipo_mensaje": "respuesta",
  "id_cliente": "cli-0001",
  "id_peticion": "req-0016",
  "estado": "ok",
  "resultado": {
    "lotes": [
      {
        "id_lote": "l-0001",
        "estado_lote": "terminado_ok",
        "codigo_salida": 0
      },
      {
        "id_lote": "l-0002",
        "estado_lote": "corriendo",
        "codigo_salida": null
      }
    ]
  },
  "error": null,
  "mensaje": "Listado de lotes consultado correctamente"
}
```

#### Estados posibles de un lote

| Estado | Descripción |
|---|---|
| `pendiente` | El lote fue recibido pero aún no inicia. |
| `corriendo` | El lote está en ejecución. |
| `terminado_ok` | El lote terminó correctamente. |
| `terminado_error` | El lote terminó con error. |
| `matado` | El lote fue detenido por solicitud del cliente. |

#### Errores posibles

| Código | Cuándo ocurre |
|---|---|
| `LOTE_NO_EXISTE` | Se envía un `id_lote` que no existe. |
| `ERROR_CONSULTA_ESTADO` | No se pudo consultar el estado del lote. |

---

### 11.3. Operación `matar`

Detiene una ejecución de lote específica.

#### Petición

```json
{
  "tipo_mensaje": "peticion",
  "id_cliente": "cli-0001",
  "id_peticion": "req-0017",
  "recurso": "lote",
  "operacion": "matar",
  "datos": {
    "id_lote": "l-0001"
  }
}
```

#### Respuesta exitosa

```json
{
  "tipo_mensaje": "respuesta",
  "id_cliente": "cli-0001",
  "id_peticion": "req-0017",
  "estado": "ok",
  "resultado": {
    "id_lote": "l-0001",
    "estado_lote": "matado"
  },
  "error": null,
  "mensaje": "Lote detenido correctamente"
}
```

#### Errores posibles

| Código | Cuándo ocurre |
|---|---|
| `LOTE_NO_EXISTE` | No existe el `id_lote`. |
| `LOTE_YA_TERMINADO` | El lote ya terminó y no puede matarse. |
| `ERROR_EJECUCION_LOTE` | No se pudo detener el proceso. |

---

### 11.4. Operaciones `suspender`, `reasumir` y `parar`

Estas operaciones controlan el estado del servicio `ejecutor`.

#### Petición para suspender

```json
{
  "tipo_mensaje": "peticion",
  "id_cliente": "cli-0001",
  "id_peticion": "req-0018",
  "recurso": "lote",
  "operacion": "suspender",
  "datos": {}
}
```

Para `reasumir` y `parar` se usa la misma estructura, cambiando el valor de `operacion`.

#### Respuesta exitosa para suspender

```json
{
  "tipo_mensaje": "respuesta",
  "id_cliente": "cli-0001",
  "id_peticion": "req-0018",
  "estado": "ok",
  "resultado": {
    "estado_servicio": "suspendido"
  },
  "error": null,
  "mensaje": "Ejecutor suspendido correctamente"
}
```

#### Respuesta exitosa para parar

```json
{
  "tipo_mensaje": "respuesta",
  "id_cliente": "cli-0001",
  "id_peticion": "req-0019",
  "estado": "ok",
  "resultado": {
    "estado_servicio": "parando"
  },
  "error": null,
  "mensaje": "Ejecutor en proceso de parada"
}
```

#### Errores posibles

| Código | Cuándo ocurre |
|---|---|
| `OPERACION_INVALIDA` | La operación no puede ejecutarse desde el estado actual. |
| `SERVICIO_TERMINADO` | El ejecutor ya terminó. |

---

## 12. Resumen de operaciones y datos

| Recurso | Operación | Servicio | Contenido de `datos` |
|---|---|---|---|
| `programa` | `guardar` | `gesprog` | `ejecutable`, `argumentos`, `ambiente`, `descripcion` |
| `programa` | `leer` | `gesprog` | `id_programa` o `{}` |
| `programa` | `actualizar` | `gesprog` | `id_programa`, `ejecutable`, `argumentos`, `ambiente`, `descripcion` |
| `programa` | `borrar` | `gesprog` | `id_programa` |
| `programa` | `suspender` | `gesprog` | `{}` |
| `programa` | `reasumir` | `gesprog` | `{}` |
| `programa` | `terminar` | `gesprog` | `{}` |
| `fichero` | `crear` | `gesfich` | `nombre_logico` opcional |
| `fichero` | `leer` | `gesfich` | `id_fichero` o `{}` |
| `fichero` | `actualizar` | `gesfich` | `id_fichero`, `ruta_origen` |
| `fichero` | `borrar` | `gesfich` | `id_fichero` |
| `fichero` | `suspender` | `gesfich` | `{}` |
| `fichero` | `reasumir` | `gesfich` | `{}` |
| `fichero` | `terminar` | `gesfich` | `{}` |
| `lote` | `ejecutar` | `ejecutor` | `entrada`, `programas`, `salida` |
| `lote` | `estado` | `ejecutor` | `id_lote` o `{}` |
| `lote` | `matar` | `ejecutor` | `id_lote` |
| `lote` | `suspender` | `ejecutor` | `{}` |
| `lote` | `reasumir` | `ejecutor` | `{}` |
| `lote` | `parar` | `ejecutor` | `{}` |

---

## 13. Consideraciones sobre `aralmac`

`aralmac` representa el área de almacenamiento del sistema.

Puede organizarse como una estructura de directorios en una implementación posterior:

```text
aralmac/
├── programas/
│   ├── p-0001/
│   │   ├── ejecutable
│   │   └── metadata.json
│   └── p-0002/
│       ├── ejecutable
│       └── metadata.json
├── ficheros/
│   ├── f-0001
│   └── f-0002
└── lotes/
    └── l-0001.json
```

`aralmac` no genera identificadores por sí mismo. Los identificadores son generados por los servicios:

```text
gesprog  → id_programa
gesfich  → id_fichero
ejecutor → id_lote
```

---

## 14. Tuberías internas del ejecutor

Las tuberías nombradas se usan para la comunicación entre los servicios principales.

Durante la ejecución de un lote, el `ejecutor` puede crear tuberías internas temporales para conectar la salida estándar de un programa con la entrada estándar del siguiente.

Estas tuberías internas:

- No transportan mensajes JSON.
- No hacen parte de la API pública.
- No necesitan estar creadas antes de iniciar el sistema.
- Existen solo durante la ejecución del lote.

Ejemplo:

```text
stdout de p-0001 → tubería interna temporal → stdin de p-0002
```

---

## 15. Ejemplo completo de uso

### 15.1. Registrar programa `actividad.py`

```json
{
  "tipo_mensaje": "peticion",
  "id_cliente": "cli-0001",
  "id_peticion": "req-0001",
  "recurso": "programa",
  "operacion": "guardar",
  "datos": {
    "ejecutable": "/home/isa/programas/actividad.py",
    "argumentos": ["sumar"],
    "ambiente": {},
    "descripcion": "Suma números recibidos por entrada estándar"
  }
}
```

Respuesta:

```json
{
  "tipo_mensaje": "respuesta",
  "id_cliente": "cli-0001",
  "id_peticion": "req-0001",
  "estado": "ok",
  "resultado": {
    "id_programa": "p-0001"
  },
  "error": null,
  "mensaje": "Programa registrado correctamente"
}
```

### 15.2. Registrar programa `dividir.py`

```json
{
  "tipo_mensaje": "peticion",
  "id_cliente": "cli-0001",
  "id_peticion": "req-0002",
  "recurso": "programa",
  "operacion": "guardar",
  "datos": {
    "ejecutable": "/home/isa/programas/dividir.py",
    "argumentos": ["2"],
    "ambiente": {},
    "descripcion": "Divide entre 2 el número recibido por entrada estándar"
  }
}
```

Respuesta:

```json
{
  "tipo_mensaje": "respuesta",
  "id_cliente": "cli-0001",
  "id_peticion": "req-0002",
  "estado": "ok",
  "resultado": {
    "id_programa": "p-0002"
  },
  "error": null,
  "mensaje": "Programa registrado correctamente"
}
```

### 15.3. Crear fichero de entrada

```json
{
  "tipo_mensaje": "peticion",
  "id_cliente": "cli-0001",
  "id_peticion": "req-0003",
  "recurso": "fichero",
  "operacion": "crear",
  "datos": {
    "nombre_logico": "numeros_entrada"
  }
}
```

Respuesta:

```json
{
  "tipo_mensaje": "respuesta",
  "id_cliente": "cli-0001",
  "id_peticion": "req-0003",
  "estado": "ok",
  "resultado": {
    "id_fichero": "f-0001"
  },
  "error": null,
  "mensaje": "Fichero creado correctamente"
}
```

### 15.4. Actualizar fichero de entrada

El archivo externo `/home/isa/datos/numeros.txt` contiene:

```text
10
20
30
```

Petición:

```json
{
  "tipo_mensaje": "peticion",
  "id_cliente": "cli-0001",
  "id_peticion": "req-0004",
  "recurso": "fichero",
  "operacion": "actualizar",
  "datos": {
    "id_fichero": "f-0001",
    "ruta_origen": "/home/isa/datos/numeros.txt"
  }
}
```

Respuesta:

```json
{
  "tipo_mensaje": "respuesta",
  "id_cliente": "cli-0001",
  "id_peticion": "req-0004",
  "estado": "ok",
  "resultado": {
    "id_fichero": "f-0001"
  },
  "error": null,
  "mensaje": "Fichero actualizado correctamente"
}
```

### 15.5. Crear fichero de salida

```json
{
  "tipo_mensaje": "peticion",
  "id_cliente": "cli-0001",
  "id_peticion": "req-0005",
  "recurso": "fichero",
  "operacion": "crear",
  "datos": {
    "nombre_logico": "resultado_final"
  }
}
```

Respuesta:

```json
{
  "tipo_mensaje": "respuesta",
  "id_cliente": "cli-0001",
  "id_peticion": "req-0005",
  "estado": "ok",
  "resultado": {
    "id_fichero": "f-0002"
  },
  "error": null,
  "mensaje": "Fichero creado correctamente"
}
```

### 15.6. Ejecutar lote

```json
{
  "tipo_mensaje": "peticion",
  "id_cliente": "cli-0001",
  "id_peticion": "req-0006",
  "recurso": "lote",
  "operacion": "ejecutar",
  "datos": {
    "entrada": "f-0001",
    "programas": ["p-0001", "p-0002"],
    "salida": "f-0002"
  }
}
```

Interpretación:

```text
f-0001 → p-0001 → p-0002 → f-0002
```

Proceso lógico:

```text
f-0001 contiene: 10, 20, 30
p-0001 suma los números: 60
p-0002 divide entre 2: 30
f-0002 queda con: 30
```

Respuesta:

```json
{
  "tipo_mensaje": "respuesta",
  "id_cliente": "cli-0001",
  "id_peticion": "req-0006",
  "estado": "ok",
  "resultado": {
    "id_lote": "l-0001",
    "estado_lote": "corriendo"
  },
  "error": null,
  "mensaje": "Lote iniciado correctamente"
}
```

### 15.7. Consultar estado del lote

```json
{
  "tipo_mensaje": "peticion",
  "id_cliente": "cli-0001",
  "id_peticion": "req-0007",
  "recurso": "lote",
  "operacion": "estado",
  "datos": {
    "id_lote": "l-0001"
  }
}
```

Respuesta:

```json
{
  "tipo_mensaje": "respuesta",
  "id_cliente": "cli-0001",
  "id_peticion": "req-0007",
  "estado": "ok",
  "resultado": {
    "id_lote": "l-0001",
    "estado_lote": "terminado_ok",
    "codigo_salida": 0
  },
  "error": null,
  "mensaje": "Estado del lote consultado correctamente"
}
```

### 15.8. Leer fichero de salida

```json
{
  "tipo_mensaje": "peticion",
  "id_cliente": "cli-0001",
  "id_peticion": "req-0008",
  "recurso": "fichero",
  "operacion": "leer",
  "datos": {
    "id_fichero": "f-0002"
  }
}
```

Respuesta:

```json
{
  "tipo_mensaje": "respuesta",
  "id_cliente": "cli-0001",
  "id_peticion": "req-0008",
  "estado": "ok",
  "resultado": {
    "id_fichero": "f-0002",
    "contenido": "30\n"
  },
  "error": null,
  "mensaje": "Fichero leído correctamente"
}
```

---

## 16. Máquinas de estado resumidas

### 16.1. `ctrllt`

```text
inicio → corriendo → terminar → terminado
```

`ctrllt` se mantiene corriendo mientras recibe y redirige peticiones.

### 16.2. `gesprog`

```text
inicio → corriendo ⇄ suspendido → terminado
```

Operaciones normales en estado `corriendo`:

```text
guardar, leer, actualizar, borrar
```

Operaciones de control:

```text
suspender, reasumir, terminar
```

### 16.3. `gesfich`

```text
inicio → corriendo ⇄ suspendido → terminado
```

Operaciones normales en estado `corriendo`:

```text
crear, leer, actualizar, borrar
```

Operaciones de control:

```text
suspender, reasumir, terminar
```

### 16.4. `ejecutor`

```text
inicio → corriendo ⇄ suspendido → parar → terminado
```

Operaciones normales:

```text
ejecutar, estado, matar
```

Operaciones de control:

```text
suspender, reasumir, parar
```

---

## 17. Conclusión

Este diseño define una API basada en mensajes JSON enviados mediante tuberías nombradas. La API permite registrar programas, registrar ficheros y ejecutar procesos de lote mediante identificadores generados por los servicios correspondientes.

El documento establece un formato común para peticiones, respuestas exitosas y respuestas de error. También separa la capa de transporte, basada en tuberías nombradas, de la estructura lógica de los mensajes JSON.

La API queda organizada en tres recursos principales:

```text
programa → gesprog
fichero  → gesfich
lote     → ejecutor
```

Esta organización permite mantener una separación clara de responsabilidades entre los componentes del sistema y proporciona una base consistente para las siguientes fases de implementación.

