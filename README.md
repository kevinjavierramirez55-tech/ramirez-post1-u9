# Ramirez Post-Contenido 1 Unidad 9

Proyecto Spring Boot del Post-Contenido de la Unidad 8 actualizado para la Unidad 9:
autenticacion con Spring Security 6, registro con BCrypt, login por formulario y
autorizacion por roles `ROLE_ADMIN` y `ROLE_USER`.

## Tecnologias

- Java 17
- Spring Boot 3.2.5
- Spring Security 6
- Spring Data JPA
- Thymeleaf + thymeleaf-extras-springsecurity6
- MySQL

## Configuracion de MySQL

Crear la base de datos y el usuario usado por `application.properties`:

```sql
CREATE DATABASE IF NOT EXISTS estudiantes_db;
CREATE USER IF NOT EXISTS 'appuser'@'localhost' IDENTIFIED BY 'apppass';
GRANT ALL PRIVILEGES ON estudiantes_db.* TO 'appuser'@'localhost';
FLUSH PRIVILEGES;
```

La aplicacion usa:

```properties
spring.datasource.url=jdbc:mysql://localhost:3306/estudiantes_db?useSSL=false&serverTimezone=UTC
spring.datasource.username=appuser
spring.datasource.password=apppass
spring.jpa.hibernate.ddl-auto=update
```

## Ejecucion

```bash
./mvnw spring-boot:run
```

En Windows:

```bash
mvnw.cmd spring-boot:run
```

Abrir:

- `http://localhost:8080/login`
- `http://localhost:8080/registro`
- `http://localhost:8080/dashboard`
- `http://localhost:8080/admin`

La conexion de MySQL esta configurada en el puerto `3306`, que es el puerto
normal de MySQL en XAMPP.

## Usuarios de prueba

Usuario normal:

- Email: se crea desde `/registro`
- Contrasenia: la que se indique en el formulario
- Rol asignado automaticamente: `ROLE_USER`

Usuario administrador:

- Email: `admin@universidad.edu`
- Contrasenia de prueba: `admin123`
- Rol: `ROLE_ADMIN`

Despues de arrancar la aplicacion una vez para que Hibernate cree la tabla
`usuarios`, insertar el administrador en MySQL:

```sql
INSERT INTO usuarios (nombre, email, contrasenia, rol, activo)
VALUES (
  'Administrador',
  'admin@universidad.edu',
  '$2a$12$6BG2hGeJVuWUvNmqGcyTH.g8bJ6niKwEASCyFgKQyCW5RSe1VjxTS',
  'ROLE_ADMIN',
  1
);
```

El hash anterior fue generado con BCrypt costo 12 para la contrasenia de prueba
`admin123`.

## Funcionalidad implementada

- `SecurityConfig` usa `SecurityFilterChain`, login personalizado y logout con
  invalidacion de sesion.
- `UsuarioDetailsService` carga usuarios desde MySQL por correo electronico.
- `UsuarioService.registrar` valida correos duplicados y guarda la contrasenia
  con `BCryptPasswordEncoder(12)`.
- `/login`, `/registro`, `/css/**` y `/js/**` son publicas.
- `/admin` y `/admin/**` requieren rol `ADMIN`.
- El resto de rutas requiere autenticacion.
- El dashboard muestra el nombre del usuario autenticado y opciones visibles por
  rol con Thymeleaf Security.

## Validacion requerida por la rubrica

1. Entrar a `/dashboard` sin sesion: debe redirigir a `/login`.
2. Registrar usuario desde `/registro`: en MySQL la columna `contrasenia` debe
   empezar por `$2a$12$`.
3. Iniciar sesion como usuario registrado: debe ver el dashboard y el mensaje de
   acceso de usuario estandar.
4. Entrar a `/admin` como `ROLE_USER`: debe mostrar `403 Forbidden`.
5. Iniciar sesion como `admin@universidad.edu` con `admin123`: debe mostrar el
   panel de administracion y la lista de usuarios.
6. Cerrar sesion: debe redirigir a `/login?logout` e invalidar la sesion.

## Capturas para entregar

Guardar en la carpeta `evidencias/`:

- `login.png`: formulario personalizado de login.
- `dashboard-user.png`: dashboard con usuario `ROLE_USER`.
- `panel-admin.png`: panel de administracion con lista de usuarios.
- `error-403-user.png`: acceso denegado al entrar a `/admin` como usuario normal.
- `bcrypt-mysql.png`: evidencia en MySQL de una contrasenia iniciando con
  `$2a$12$`.
