# Laboratorio: Desarrollo y Pruebas de una API REST con PHP, Arquitectura MVC y Autenticación JWT

## Información Institucional
* **Universidad:** Universidad Tecnológica de Panamá
* **Facultad:** Facultad de Ingeniería en Sistemas Computacionales
* **Carrera:** Licenciatura en Desarrollo y Gestión de Software
* **Asignatura:** Desarrollo de Software VII / Ingeniería de Software Aplicada II
* **Instructor:** Ing. Irina Fong
* **Semestre:** I Semestre 2026
* **Estudiantes:** Luis Jimenez & Brian Lee
* **Cédulas:** 8-1018-1285 / 8-1031-2047

---

## Descripción del Proyecto

Lab_API_REST_JWT consiste en un servicio web desarrollado para la administración optimizada de un catálogo de productos. El sistema interactúa directamente con una base de datos MySQL denominada `api_rest_jwt` y está diseñado bajo las pautas de una arquitectura enteramente stateless (sin estado). Al prescindir del manejo de sesiones tradicionales en el servidor, la API delega la validación de la identidad al cliente. Por lo tanto, cualquier solicitud dirigida a un endpoint protegido requiere obligatoriamente un token JSON Web Token (JWT) adjunto en el encabezado `Authorization`.

El flujo operativo básico establece que un usuario con privilegios de administrador debe autenticarse en primera instancia para generar un token firmado. Una vez obtenido, dicho token sirve como credencial de acceso para efectuar las operaciones de consulta, inserción, actualización o eliminación de registros.

## Objetivo General

Construir una API REST sólida empleando PHP que permita la ejecución del ciclo completo de operaciones CRUD sobre la entidad de productos. El desarrollo asegura la integridad de la información mediante el uso de esquemas de autenticación basados en JWT, el procesamiento unificado de peticiones en formato JSON, la implementación de contraseñas seguras y la validación funcional de los endpoints a través de Postman.

## Estructura del Directorio

La organización del código fuente sigue un patrón limpio orientado a la separación de responsabilidades, aislando los controladores, las utilidades de respuesta y la conexión al motor de datos.

**(Insertar Imagen: Estructura de carpetas del proyecto)**

### Desglose de Componentes Principales

<img width="299" height="528" alt="Captura de pantalla 2026-06-30 000624" src="https://github.com/user-attachments/assets/af1bbf9f-11a5-4d8e-bbf7-9669bf17249d" />


Tecnologías utilizadas 
* PHP para la lógica del backend. 
* MySQL para almacenar usuarios y productos. 
* PDO para realizar consultas seguras a la base de datos. 
* Composer como gestor de dependencias. 
* firebase/php-jwt para generar y validar tokens JWT. 
* Postman para probar los endpoints de la API. 
* JSON como formato de intercambio de datos. 
## Arquitectura de Control y Flujo de Peticiones

## Base de Datos

La persistencia de los datos se gestiona a través de una base de datos MySQL denominada `api_rest_jwt`, la cual está estructurada en torno a dos tablas principales:

* **usuarios**: Destinada a almacenar el perfil del usuario administrador junto con sus credenciales de acceso debidamente protegidas.
* **productos**: Encargada de registrar y centralizar todo el catálogo de artículos administrados por la API.

Con el objetivo de garantizar la seguridad del sistema y evitar vulnerabilidades, las contraseñas nunca se guardan en texto plano. En su lugar, el proceso de registro las encripta de forma segura mediante la función `password_hash()` de PHP, utilizando estrictamente el algoritmo **PASSWORD_BCRYPT**.

El núcleo de la aplicación utiliza el archivo `public/index.php` para interceptar las solicitudes entrantes. Mediante una estructura de control condicional (`switch`), la API evalúa el método HTTP de la petición y delega la ejecución a la función pertinente dentro de los controladores.

<img width="165" height="126" alt="Captura de pantalla 2026-06-30 002033" src="https://github.com/user-attachments/assets/4cc0b491-5c9e-4f7a-8bc5-69235024fac0" />

<img width="767" height="159" alt="Captura de pantalla 2026-06-30 002158" src="https://github.com/user-attachments/assets/e8507f20-f03f-4907-85fd-22c88cce1c71" />

<img width="643" height="204" alt="Captura de pantalla 2026-06-30 002222" src="https://github.com/user-attachments/assets/1cd925be-d0fe-4df1-a5e6-b70c5f235241" />

## Arquitectura de Control y Flujo de Peticiones

El núcleo de la aplicación utiliza el archivo `public/index.php` para interceptar las solicitudes entrantes. Mediante una estructura de control condicional (`switch`), la API evalúa el método HTTP de la petición y delega la ejecución a la función pertinente dentro de los controladores.

<img width="454" height="595" alt="Captura de pantalla 2026-06-30 000717" src="https://github.com/user-attachments/assets/62f6032e-84d7-4808-8d69-d6820d3498b3" />

## Persistencia y Modelo de Datos

La persistencia de los datos se gestiona a través de una base de datos MySQL denominada `api_rest_jwt`, la cual está estructurada en torno a dos tablas principales:

* **usuarios**: Destinada a almacenar el perfil del usuario administrador junto con sus credenciales de acceso debidamente protegidas.
* **productos**: Encargada de registrar y centralizar todo el catálogo de artículos administrados por la API.

Con el objetivo de garantizar la seguridad del sistema y evitar vulnerabilidades, las contraseñas nunca se guardan en texto plano. En su lugar, el proceso de registro las encripta de forma segura mediante la función `password_hash()` de PHP, utilizando estrictamente el algoritmo **PASSWORD_BCRYPT**.

## Gestión de Endpoints y Pruebas en Postman

### 1. Autenticación y Generación de Token
Para interactuar con las rutas restringidas del sistema, es obligatorio obtener un token válido mediante el envío de credenciales en formato JSON al endpoint de login.

* **Endpoint**: `index.php?route=login`
* **Método**: `POST`

**(Insertar Imagen: Petición de Login exitosa en Postman y respuesta con JWT)**

### 2. Operaciones sobre el Catálogo de Productos
Cualquier solicitud subsiguiente orientada a la manipulación del catálogo debe adjuntar la cabecera HTTP correspondiente: `Authorization: Bearer <TOKEN_GENERADO>`.

| Método | Endpoint | Acción Realizada | Requiere Token |
| :--- | :--- | :--- | :--- |
| POST | index.php?route=login | Inicio de sesión y entrega de JWT | No |
| GET | index.php | Recuperar lista completa o buscar por ID | Sí |
| POST | index.php | Dar de alta un nuevo producto | Sí |
| PUT | index.php?id=ID | Modificar datos de un registro existente | Sí |
| DELETE | index.php?id=ID | Remover de forma permanente un producto | Sí |

## Seguridad y Autenticación con JWT

El sistema implementa un esquema de seguridad basado en JSON Web Tokens (JWT) para restringir el acceso a los recursos privados. El flujo de autenticación requiere que el cliente realice una solicitud inicial de inicio de sesión con las credenciales del administrador; si la verificación en el servidor es exitosa, este responderá emitiendo un token firmado.

Este token funciona como la credencial de identidad del cliente y debe ser adjuntado obligatoriamente en el encabezado `Authorization` de cada una de las peticiones subsecuentes dirigidas a endpoints protegidos.

El formato estructurado para la cabecera en las pruebas de Postman se define de la siguiente manera:

```http
{
    "success": false,
    "message": "Usuario y contraseña son obligatorios."
}
```
###  Autenticación y Generación de Token

El acceso a las funciones restringidas de la API requiere la obtención previa de un token de seguridad a través del endpoint de inicio de sesión. Este componente está diseñado para recibir las credenciales de acceso (usuario y contraseña) encapsuladas en formato JSON. 

Una vez recibida la solicitud, el sistema realiza la validación de la identidad comparando de forma segura la clave ingresada frente al hash almacenado en la base de datos, procesando este control mediante la función nativa **password_verify()** de PHP.
POST  http://localhost/API_REST_JWT\Lab_API_REST_JWT (1)\Lab_API_REST_JWT\public/index.php?route=login

```http
{
    "usuario": "brian",
    "password": "123456"
}
```
<img width="1082" height="692" alt="image" src="https://github.com/user-attachments/assets/3f669cea-cef6-476d-bba6-9f6315f96dc9" />

### Componente de Utilidad: Gestión y Validación de Hashes

El proyecto incorpora un script especializado denominado `hash.php`, el cual provee una interfaz funcional dedicada exclusivamente a la generación de hashes criptográficos mediante el algoritmo **BCRYPT**, así como a la posterior verificación de correspondencia entre contraseñas en texto plano y sus registros encriptados. 

Este módulo permite dar cumplimiento estricto a los criterios de seguridad del sistema, centralizando el comportamiento de las funciones nativas **password_hash()** y **password_verify()** a través de un entorno accesible para pruebas independientes.

<img width="688" height="705" alt="image" src="https://github.com/user-attachments/assets/c52e5524-766d-4ba2-b9a9-cd7e5d84c854" />

<img width="678" height="688" alt="image" src="https://github.com/user-attachments/assets/20d2fb8f-4460-42e7-9606-c02bff2bdbc8" />

<img width="721" height="149" alt="image" src="https://github.com/user-attachments/assets/08a09bfa-88bb-4f84-beb4-d70f950d5e8a" />

### metodo Get
El método GET permite consultar los productos registrados. Esta petición requiere enviar el token JWT en la 
pestaña Authorization de Postman como Bearer Token. 
GET http://localhost/API_REST_JWT/Lab_API_REST_JWT%20(1)/Lab_API_REST_JWT/public/index.php?route=products


<img width="1090" height="944" alt="image" src="https://github.com/user-attachments/assets/28bdbe32-bf7f-41aa-8988-2b4a38eac34a" />


### Metodo Post

El método POST permite crear un nuevo producto. El cuerpo de la petición se envía en formato JSON con 
código, producto, precio y cantidad.

http://localhost/API_REST_JWT/Lab_API_REST_JWT%20(1)/Lab_API_REST_JWT/public/index.php?route=products
```http
{
    "codigo": "A004",
    "producto": "Webcam HD",
    "precio": 45.50,
    "cantidad": 10
}
```
<img width="1367" height="826" alt="image" src="https://github.com/user-attachments/assets/b9bbae26-16b5-4724-b88e-7d9941a036fe" />

### Prueba Metodo PUT

El método PUT permite actualizar un producto existente. El ID del producto se envía por parámetro en la URL y 
los nuevos datos se envían en formato JSON.
PUT http://localhost/API_REST_JWT/Lab_API_REST_JWT%20(1)/Lab_API_REST_JWT/public/index.php?route=products&id=2

<img width="1092" height="603" alt="image" src="https://github.com/user-attachments/assets/aa6702e2-b700-4576-9a98-e544836c5883" />

### Metodo DELETE
El método DELETE permite eliminar un producto existente. El ID del producto se envía como parámetro en la 
URL. Esta operación también requiere token JWT.
DELETE http://localhost/API_REST_JWT/Lab_API_REST_JWT%20(1)/Lab_API_REST_JWT/public/index.php?route=products&id=3

<img width="1085" height="655" alt="image" src="https://github.com/user-attachments/assets/4e2dc4f3-6e06-47f3-bd48-0187b48cae0b" />

### Controlador Centralizado (Front Controller) y Enrutamiento

El archivo `public/index.php` actúa como el núcleo operacional del sistema bajo el patrón de un Front Controller. Este componente se encarga de interceptar de forma unificada todas las solicitudes entrantes, extraer y validar la firma del token JWT en primera instancia, y posteriormente coordinar el flujo del sistema. 

Haciendo uso de una estructura de control condicional `switch`, la API evalúa el método HTTP de la petición para delegar de manera dinámica la ejecución hacia la función correspondiente del controlador de productos.

* GET llama al método get() del ProductController. 
* POST llama al método post() del ProductController. 
* PUT llama al método put() del ProductController. 
* DELETE llama al método delete() del ProductController.

<img width="778" height="348" alt="image" src="https://github.com/user-attachments/assets/dd28df82-8afa-4d98-a964-d0438c2b9d58" />

### Manejo y Estandarización de Errores

El sistema incorpora un bloque centralizado de control de excepciones que traduce las incidencias de ejecución en respuestas estructuradas en formato JSON, acompañadas de sus respectivos códigos de estado HTTP estándar. Esto garantiza un comportamiento predecible y facilita la depuración desde el lado del cliente:

* **400 Bad Request**: Se genera cuando los datos de entrada proporcionados en la solicitud no superan los criterios de validación obligatorios, adjuntando un arreglo detallado bajo la propiedad `errors`.
* **401 Unauthorized**: Se emite de forma automática si la petición carece de la cabecera de autenticación, o bien si el token JWT enviado es inválido o ha expirado.
* **404 Not Found**: Es retornado cuando se intenta interactuar con un identificador de producto o recurso que no existe en el sistema.
* **201 Created**: Código de éxito devuelto tras confirmar la inserción correcta de un nuevo producto en la base de datos.

<img width="1081" height="670" alt="image" src="https://github.com/user-attachments/assets/19426a2c-f60e-46e7-90a9-4eed9fffbac2" />

### Dificultades & Problemas

Durante el desarrollo de la API REST con JWT se enfrentaron múltiples desafíos técnicos que requirieron varias recreaciones del proyecto. Problema inicial: El archivo composer.json tenía configurado el namespace Waikilee\ApiJwtSoftware, pero todos los archivos PHP utilizaban namespace App, causando que Composer no encontrara las clases. Solución: Se actualizó el autoload de Composer para que apuntara correctamente a App. Luego surgió un conflicto con la configuración de MariaDB: WAMP tenía dos instancias de bases de datos (MySQL en puerto 3306 y MariaDB en puerto 3307), y el config.php estaba mal estructurado con define() en lugar de retornar un array. Se corrigió la configuración y se apuntó al puerto 3307 correcto, creando el usuario jwt_app con las credenciales adecuadas. El mayor obstáculo fue Apache mod_fcgid bloqueando el header Authorization, lo que impedía que los tokens llegaran a PHP. Se intentaron múltiples soluciones: .htaccess con SetEnvIf, modificar AuthService.php con fallbacks, pero ninguna funcionó completamente. Durante estos intentos, se introdujeron errores de BOM (Byte Order Mark) en los archivos PHP al usar PowerShell, causando que PHP rechazara la declaración namespace en la línea 2. Se reconstruyó el proyecto completo desde cero extrayendo un ZIP limpio y descartando la carpeta corrupta. Finalmente, el proyecto se estabilizó con: autenticación JWT funcional (login y registro ), validación de tokens, métodos CRUD completos (GET, POST, PUT, DELETE) con códigos HTTP correctos (200, 201, 400, 401, 405), control de errores centralizado en public/index.php con estructura switch, y una interfaz hash.php para generar y validar contraseñas con BCRYPT. El aprendizaje clave fue que la limpieza del proyecto y evitar herramientas que introducen caracteres especiales fue más efectivo que intentar parches incrementales.

### Conclucion

El desarrollo de Lab_API_REST_JWT ha demostrado que la construcción de una API REST segura y funcional en PHP requiere no solo la implementación correcta de patrones de arquitectura moderna, sino también una comprensión profunda de los desafíos operacionales en entornos de desarrollo como WAMP. A través de múltiples iteraciones y resolución de conflictos técnicos complejos relacionados con la configuración de Composer, bases de datos y restricciones de seguridad en Apache, se logró materializar una solución completamente operativa.El sistema implementa exitosamente la autenticación basada en JSON Web Tokens con cifrado de contraseñas mediante BCRYPT, proporcionando un nivel de seguridad adecuado para la protección de recursos sensibles. La arquitectura de controlador frontal (Front Controller) centraliza el procesamiento de peticiones HTTP, garantizando un flujo uniforme y predecible. El manejo estructurado de errores mediante códigos HTTP estándar y respuestas JSON facilita la integración con clientes y herramientas de prueba como Postman.Los métodos CRUD implementados (GET, POST, PUT, DELETE) funcionan correctamente con validación de tokens, demostrando que el sistema es capaz de mantener la integridad de los datos mientras controla el acceso a través de autenticación. La interfaz hash.php proporciona una herramienta adicional para validación independiente de contraseñas, reforzando las prácticas de seguridad.El aprendizaje fundamental durante este proyecto fue que la estabilidad y mantenibilidad del código dependen más de decisiones arquitectónicas sólidas y limpias que de intentos incrementales de parches. La reconstrucción completa del proyecto desde cero, evitando herramientas que introducen caracteres especiales y manteniendo la separación clara de responsabilidades, resultó ser el enfoque más efectivo.Lab_API_REST_JWT representa una base sólida para futuras extensiones, tales como autorizaciones basadas en roles, implementación de refresh tokens, rate limiting, o integración con servicios adicionales. El proyecto cumple satisfactoriamente con los objetivos académicos establecidos y demuestra competencia en el desarrollo de servicios web modernos.
