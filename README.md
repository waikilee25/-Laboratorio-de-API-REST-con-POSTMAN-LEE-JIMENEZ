# Laboratorio: Desarrollo y Pruebas de una API REST con PHP, Arquitectura MVC y Autenticación JWT

## Información Institucional
* **Universidad:** Universidad Tecnológica de Panamá
* **Facultad:** Facultad de Ingeniería en Sistemas Computacionales
* **Carrera:** Licenciatura en Desarrollo y Gestión de Software
* **Asignatura:** Desarrollo de Software VII / Ingeniería de Software Aplicada II
* **Instructor:** Ing. Irina Fong
* **Semestre:** I Semestre 2026

---

## 1. Introducción y Objetivos
Este proyecto consiste en el diseño, desarrollo y evaluación de una API RESTful robusta construida en PHP nativo, integrada con un motor de bases de datos relacionales MySQL. 

El sistema sigue de forma estricta el patrón arquitectónico Modelo-Vista-Controlador (MVC), garantizando una separación limpia de responsabilidades al aislar las conexiones a bases de datos, lógica de negocio, formateo JSON y utilitarios de seguridad en módulos independientes.

### Objetivos Clave:
1. **Arquitectura Limpia:** Implementar un flujo desacoplado (MVC) para maximizar la escalabilidad del backend.
2. **Seguridad Stateless:** Implementar una arquitectura sin estado empleando JSON Web Tokens (JWT) para restringir rutas críticas de forma dinámica.
3. **Protección Criptográfica:** Procesar y encriptar contraseñas administrativas a nivel de almacenamiento usando el algoritmo robusto BCRYPT.
4. **Auditoría HTTP:** Validar de forma autónoma el ciclo de vida de peticiones y respuestas mediante la herramienta Postman utilizando códigos de estado HTTP estandarizados.

---

## 2. Estructura del Proyecto (PSR-4 compliant)
El proyecto utiliza el estándar de carga automática PSR-4 gestionado a través de Composer. Las clases lógicas residen dentro del espacio de nombres (namespace) `App` mapeado directamente al directorio `/src`.


API-JWT-SOFTWARE/
├── public/                 # Directorio de acceso público del servidor web
│   ├── index.php           # Front Controller (Punto de entrada único de la API)
│   └── hash.php            # Interfaz utilitaria para control de hashes criptográficos
├── src/                    # Código lógico del backend (Autoloading PSR-4)
│   ├── DB.php              # Conexión persistente y segura a MySQL utilizando PDO
│   ├── AuthService.php     # Gestión criptográfica, codificación y decodificación de JWT
│   ├── UserController.php  # Controlador para el flujo de autenticación y registro
│   ├── ProductController.php # Controlador para la persistencia del CRUD de productos
│   └── Response.php        # Utilidad estandarizada para salida de respuestas en JSON
├── sql/
│   └── api_rest_jwt.sql    # Scripts DDL/DML de la base de datos y semillas iniciales
├── vendor/                 # Dependencias externas administradas por Composer
├── .gitignore              # Exclusiones de Git para el blindaje de credenciales locales
├── composer.json           # Manifiesto de dependencias y mapeos PSR-4 del sistema
└── config.php              # Variables globales de base de datos y llaves simétricas JWT



3. Inicialización del Entorno y Dependencias
3.1.Para reconstruir o inicializar el ecosistema de dependencias profesionales, ejecute los siguientes comandos en la raíz de la aplicación:

Inicialización del Manifiesto:
composer init
 Durante el asistente interactivo se definió el tipo como project para una aplicación final auto-contenida y se configuró el mapeo PSR-4 asociando el namespace App a la carpeta src/.

3.2. Instalación de la Librería de Seguridad:
composer require firebase/php-jwt
Este comando descarga la librería oficial firebase/php-jwt dentro del directorio /vendor y genera automáticamente el cargador dinámico de clases autoload.php.

4. Modelo de Seguridad e Infraestructura
Actividad A: Blindaje de Credenciales (config.php)
Los secretos criptográficos de firma y accesos a bases de datos se externalizan en el archivo raíz config.php, excluido de los sistemas de control de versiones públicos mediante reglas estrictas en .gitignore.

<?php
// config.php - Blindaje de Datos Críticos
define('JWT_SECRET_KEY', 'TU_LLAVE_SECRETA_SUPER_COMPLEJA_ALTA_ENTROPIA');
define('JWT_ALGORITHM', 'HS256');

define('DB_HOST', 'localhost');
define('DB_NAME', 'api_jwt_software');
define('DB_USER', 'root');
define('DB_PASS', '');


Actividad B: Hashing de Contraseñas (Algoritmo BCRYPT)
Las contraseñas de cuentas administrativas jamás se almacenan en texto plano en la tabla usuarios.

Registro/Inicialización: Se aplica password_hash($password, PASSWORD_BCRYPT) para generar un hash irreversible de 60 caracteres en la base de datos.

Autenticación (Login): Durante la verificación en UserController, se emplea password_verify($password_plano, $password_hasheada) para validar las firmas digitales de autenticación.

5. Flujo del Ciclo Stateless e Intercepción Centralizada
La API opera bajo principios Stateless (sin estado). El servidor no almacena sesiones persistentes en memoria entre peticiones concurrentes. Cada llamada entrante es independiente y requiere obligatoriamente adjuntar el token JWT en las cabeceras HTTP de la solicitud.

Arquitectura Front Controller (public/index.php)
El archivo public/index.php actúa como el Punto de Entrada Único del sistema, interceptando cada verbo HTTP (GET, POST, PUT, DELETE) para evaluar la seguridad perimetral antes de despachar a la lógica del CRUD:

[ Cliente HTTP (Postman) ]
            │
            ▼
┌─────────────────────────────────────────────────────────┐
│     PUNTO DE ENTRADA ÚNICO (public/index.php)           │
├─────────────────────────────────────────────────────────┤
│ 1. Carga el Autoload de Composer                       │
│ 2. Extrae Cabecera: Authorization: Bearer <TOKEN>        │
│ 3. Instancia AuthService para validar firma JWT         │
└───────────────────────────┬─────────────────────────────┘
                            │
              ┌─────────────┴─────────────┐
              │ ¿Ruta Pública (/login)?   │
              └─────────────┬─────────────┘
                            │
               ┌────────────┴────────────┐
               │                         │
            [ SÍ ]                    [ NO ]
               │                         │
               ▼                         ▼
   ┌───────────────────────┐ ┌───────────────────────────────┐
   │ Despacha a:           │ │ ¿JWT es Válido y Vigente?     │
   │ UserController        │ └───────────┬───────────┬───────┘
   │ (Permite autenticar)  │             │           │
   └───────────────────────┘          [ SÍ ]       [ NO ]
                                         │           │
                                         ▼           ▼
                           ┌───────────────────┐ ┌───────────────────┐
                           │ Procesa verbos a: │ │ Retorna código:   │
                           │ ProductController │ │ HTTP 401          │
                           │ (GET, POST, PUT,  │ │ Unauthorized      │
                           │  DELETE)          │ └───────────────────┘
                           └───────────────────┘


6. Plan de Auditoría e Interacción en Postman
El laboratorio exige la demostración en Postman de dos escenarios fundamentales de control:

Escenario Negativo: Acceso Sin Token o Inválido
Acción: Realizar una petición a una ruta CRUD protegida (ej: GET http://localhost/.../public/index.php) omitiendo el encabezado de autorización.

Respuesta del Servidor:

Código HTTP: 401 Unauthorized

Cuerpo del JSON:
{
    "success": false,
    "message": "Acceso no autorizado o token inválido.",
    "error": "Token no enviado."
}



Escenario Positivo: Ciclo de Operaciones Seguro
Paso 1: Login y Obtención del Token JWT
Endpoint: POST http://localhost/.../public/index.php?route=login (o el endpoint configurado para autenticación)

ejemplo Cuerpo de la Petición (JSON en raw):
{
    "usuario": "admin",
    "password": "tu_contraseña_segura"
}

Respuesta Esperada: 200 OK con el token JWT firmado de vuelta.

Paso 2: Consumo Seguro del CRUD de Productos
Acción: Configurar una petición HTTP (GET, POST, PUT o DELETE) hacia las rutas de inventario de productos.

Configuración de Cabecera Obligatoria: Pestaña Authorization -> Seleccionar Bearer Token -> Pegar el string JWT generado en el Paso 1.
(Equivale a inyectar la cabecera: Authorization: Bearer <TOKEN_GENERADO>).

Respuesta Esperada: Execution exitosa con códigos de estado correspondientes (200 OK para consultas/actualizaciones o 201 Created al registrar elementos).
