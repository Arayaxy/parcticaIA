# AGENTS.md - Proyecto Movie App API REST

## Rol del agente
Actúa como un **Senior Full-Stack Engineer & Tech Lead**. El objetivo es desarrollar y mantener código limpio, escalable y tipado bajo principios de arquitectura limpia para el stack **Node.js/Express** y **PostgreSQL**.

Defiende estrictamente **TDD (Test-Driven Development)**: antes de implementar o modificar lógica relevante, define o actualiza pruebas que describan el comportamiento esperado.

## Alcance
Estas instrucciones aplican a todo el repositorio. El proyecto corresponde al backend de **Movie App**, centrado en una API REST con lógica de servidor, persistencia de datos, gestión de ficheros y seguridad.

## Descripción general del proyecto
En esta fase se debe desarrollar exclusivamente la lógica de servidor de una API REST robusta usando **Node.js** y **Express**.

Todas las respuestas del servidor deben estar estructuradas estrictamente en formato **JSON** y deben usar códigos de estado HTTP adecuados.

## Tecnologías requeridas
- **Entorno y framework:** Node.js y Express.
- **Base de datos:** PostgreSQL.
- **Autenticación:** JWT (JSON Web Tokens).
  - Opcionalmente se puede integrar autenticación con Firebase.
- **Validación:** Express-Validator.
- **Gestión de archivos:** Multer.
- **Almacenamiento de imágenes:** Cloudinary.
- **Documentación de API:** OpenAPI integrado con Swagger.

## Base de datos y modelado

### Usuarios
La tabla de usuarios debe almacenar:
- `id`.
- `nombre`.
- `email`.
- `contraseña`, almacenada obligatoriamente con hashing/cifrado.
- `rol`, con valores estrictos: `user` o `admin`.
- Relación con sus películas favoritas.

### Películas
La tabla de películas debe almacenar:
- `id`, usando un identificador público cifrado o codificado para exponerlo en las rutas y respuestas.
- `imdbId`, como referencia externa principal de IMDb.
- `título completo`.
- `imagen`, como string con la URL o identificador de Cloudinary.
- `año`.
- `director`.
- `género`.
- `duración`.

## Endpoints de la API REST

### Autenticación y gestión de usuarios (`/api/auth`)

#### `POST /api/auth/signup`
Registro de usuarios locales.

Campos requeridos:
- `nombre`.
- `email`.
- `password`.

#### `POST /api/auth/login`
Autenticación local.

Debe devolver:
- JWT firmado.
- Datos del usuario, incluyendo el rol.

### Rutas de películas para usuarios (`/api/movies`)
Todas estas rutas deben estar protegidas por middleware de verificación de JWT.

#### `GET /api/movies/search?title=...`
Debe buscar coincidencias de títulos dentro de la base de datos local, es decir, el catálogo del administrador.

Integración externa:
- Si no se encuentra la película localmente, el backend debe realizar una petición interna a OMDB.
- Las películas externas deben usar `imdbId` como referencia para crearlas posteriormente en la base de datos local.
- El JSON resultante debe incluir obligatoriamente la propiedad `source` con valor `local` o `external`.

#### `GET /api/movies/favorites`
Devuelve el listado de películas favoritas guardadas por el usuario autenticado.

#### `GET /api/admin/movies/:id`
Vista detalle de una película obtenida por su id.

#### `POST /api/movies/favorites`
Vincula una película a los favoritos del usuario.

Si la película proviene de una API externa, el backend debe crearla primero en la base de datos local de forma automatizada antes de guardarla en favoritos.

#### `DELETE /api/movies/favorites/:id`
Elimina la película de los favoritos del usuario autenticado.

### Rutas de administrador (`/api/admin/movies`)
Todas estas rutas deben estar protegidas por doble middleware:
1. Verificación de JWT.
2. Verificación de rol `admin`.

#### `GET /api/admin/movies`
Lista la totalidad de las películas almacenadas en la base de datos local.

#### `GET /api/admin/movies/:id`
Vista detalle de una película obtenida por su id.

#### `POST /api/admin/movies`
Da de alta una nueva película.

Requisitos:
- Usar Multer para procesar el archivo de imagen de la carátula.
- Subir y almacenar las imágenes mediante Cloudinary.
- Usar Express-Validator para validar los campos de texto.

#### `PUT /api/admin/movies/:id`
Modifica los datos o la imagen de una película existente.

Debe contar con las mismas validaciones que la creación.

#### `DELETE /api/admin/movies/:id`
Elimina físicamente la película de la base de datos.

Importante:
- Debe limpiar y desasociar de forma automática las listas de favoritos de cualquier usuario que tuviera guardada dicha película.

## Requisitos generales de implementación
- Todas las respuestas deben ser JSON.
- Usar códigos de estado HTTP adecuados.
- Proteger las rutas privadas mediante JWT.
- Restringir rutas administrativas mediante verificación de rol `admin`.
- Hashear siempre las contraseñas antes de almacenarlas.
- Validar entradas con Express-Validator.
- Gestionar subidas de imágenes con Multer.
- Almacenar las imágenes en Cloudinary.
- No exponer secretos ni credenciales reales en el repositorio.
- Documentar funciones y controladores principales con comentarios y JSDOC.

## Requisitos de entrega

### Documentación en código
- El código debe estar debidamente comentado.
- Las funciones y controladores principales del servidor deben usar JSDOC.

### Especificación de la API
- Documentar la API usando OpenAPI.
- Integrar Swagger para exponer y probar la documentación de la API.

### Pruebas de la API
- Incluir archivo o enlace a la colección de Postman con todas las rutas creadas y organizadas.

## Pautas para el README del backend
El repositorio debe incluir un `README.md` en la raíz, correctamente formateado, con las siguientes secciones:

1. **Descripción del proyecto**.
2. **Tecnologías**.
3. **Setup local**:
   - Pasos para instalar dependencias.
   - Pasos para configurar el entorno.
   - Pasos para ejecutar el servidor.
4. **Variables de entorno**:
   - Plantilla de variables, incluyendo puerto, URI de conexión y claves JWT.
5. **Tabla de endpoints**:
   - Método HTTP.
   - Ruta.
   - Descripción.
   - Requisitos de autenticación y roles.
6. **Credenciales de prueba**:
   - 1 administrador.
   - 2 usuarios.
7. **Enlace al tablero ágil**:
   - Trello o GitHub Projects público.

## Presentación técnica

### Arquitectura de la API
- Diseño de endpoints.
- Estructura de la base de datos relacional.

### Seguridad y control de acceso
- Demostración del funcionamiento del middleware de autenticación JWT.
- Demostración del middleware de restricción por roles `user` y `admin`.

### Demostración práctica
- Pruebas en vivo desde Postman.
- Validación del funcionamiento de endpoints.
- Validaciones con Express-Validator.
- Subida de archivos con Multer.
- Almacenamiento de imágenes con Cloudinary.

## Convenciones de desarrollo recomendadas
- Mantener controladores, modelos, rutas, middlewares y servicios separados por responsabilidad.
- Centralizar el manejo de errores y devolver siempre errores en JSON.
- Usar nombres de ramas descriptivos, preferiblemente con prefijos `feature/` o `fix/`.
- Mantener commits pequeños, claros y relacionados con una sola intención.
- Actualizar la documentación cuando se añadan o modifiquen endpoints.
- Añadir o actualizar pruebas/manuales de Postman cuando cambie el comportamiento de la API.
