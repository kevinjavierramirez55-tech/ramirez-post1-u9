# Gestión de Cursos y Estudiantes - Relación ManyToMany con JPA

## Autor

- **Nombre:** Kevin Ramirez  
- **Código:** 02220131008  
- **Programa:** Ingeniería de Sistemas  
- **Unidad:** 8 Persistencia con JPA/Hibernate  
- **Actividad:** Post-Contenido 2  
- **Fecha:** 20/04/2026  

Aplicación Web Java con Spring Boot que implementa una relación @ManyToMany entre las entidades Curso y Estudiante, utilizando JPA, Hibernate y MySQL.

---

## Descripción del Proyecto

Este proyecto extiende el sistema desarrollado en el Post-Contenido 1, agregando la gestión de cursos y la relación entre estudiantes y cursos.

Se implementa una relación Muchos a Muchos (N:M) entre las entidades Curso y Estudiante, permitiendo que:

- Un estudiante esté inscrito en varios cursos.
- Un curso tenga múltiples estudiantes.

Además, se optimiza el rendimiento evitando el problema N+1 mediante el uso de **JOIN FETCH** en consultas JPA.

---

## Prerrequisitos

Antes de ejecutar el proyecto, se debe contar con:

- Proyecto del Post-Contenido 1 completamente funcional.
- JDK 17 o superior.
- MySQL 8.x en ejecución.
- Maven o wrapper `mvnw`.
- Conocimientos en:
  - JPA (@ManyToMany, @JoinTable)
  - Spring Boot y Thymeleaf

---

## Estructura del Proyecto


estudiantes/
├── src/main/java/com/universidad/estudiantes/
│ ├── EstudiantesApplication.java
│ ├── controller/
│ │ ├── EstudianteController.java
│ │ └── CursoController.java
│ ├── model/
│ │ ├── Estudiante.java
│ │ └── Curso.java
│ ├── repository/
│ │ ├── EstudianteRepository.java
│ │ └── CursoRepository.java
│ └── service/
│ ├── EstudianteService.java
│ └── CursoService.java
├── src/main/resources/
│ ├── application.properties
│ └── templates/
│ ├── estudiantes/
│ └── cursos/
│ ├── lista.html
│ ├── formulario.html
│ └── inscribir.html
├── pom.xml
└── mvnw


---

## Arquitectura del Proyecto

**Modelo:**
- Estudiante: entidad JPA existente.
- Curso: nueva entidad con relación ManyToMany.

**Repositorio:**
- CursoRepository: incluye consultas con JOIN FETCH.
- EstudianteRepository: heredado del proyecto anterior.

**Servicio:**
- CursoService: gestiona la relación entre cursos y estudiantes.

**Controlador:**
- CursoController: maneja las rutas de cursos e inscripciones.

**Vista:**
- Thymeleaf para formularios y listados.

---

## Relación ManyToMany

Se implementa una relación bidireccional entre Curso y Estudiante.

### Lado propietario (Curso)


@ManyToMany
@JoinTable(
name = "curso_estudiante",
joinColumns = @JoinColumn(name = "curso_id"),
inverseJoinColumns = @JoinColumn(name = "estudiante_id")
)
private Set<Estudiante> estudiantes;


### Lado inverso (Estudiante)


@ManyToMany(mappedBy = "estudiantes")
private Set<Curso> cursos;


### Helper Methods

Se implementan métodos para mantener sincronizada la relación:


public void agregarEstudiante(Estudiante e) {
this.estudiantes.add(e);
e.getCursos().add(this);
}

public void quitarEstudiante(Estudiante e) {
this.estudiantes.remove(e);
e.getCursos().remove(this);
}


---

## Optimización con JOIN FETCH

Para evitar el problema N+1:


@Query("SELECT c FROM Curso c LEFT JOIN FETCH c.estudiantes")
List<Curso> findAllConEstudiantes();


---

## Funcionalidades Implementadas

- Creación de cursos.
- Listado de cursos con estudiantes inscritos.
- Inscripción de estudiantes en cursos.
- Desinscripción de estudiantes.
- Relación bidireccional sincronizada.
- Persistencia en MySQL.
- Optimización de consultas con JOIN FETCH.

---

## Instrucciones de Ejecución

1. Clonar el repositorio:


https://github.com/kevinjavierramirez55-tech/ramirez-post2-u8.git


2. Configurar MySQL (usar la misma base del Post-Contenido 1).

3. Ejecutar la aplicación:


mvn spring-boot:run


4. Acceder en el navegador:


http://localhost:8080/cursos


---

## Checkpoint de Verificación

- Hibernate crea la tabla `curso_estudiante`.
- Se pueden crear cursos correctamente.
- Se pueden inscribir estudiantes en cursos.
- La tabla `curso_estudiante` se llena con los datos.
- Se pueden desinscribir estudiantes.
- No se presentan múltiples consultas innecesarias (evita N+1).

---

## Capturas del Proyecto

Las capturas se encuentran en la carpeta `evidencias/`.

### Hibernate genera tabla curso_estudiante y listar

![lista](evidencias/hibernate%20genera%20tabla%20curso_estudiante.png)

### Inscripción de estudiantes

![inscribir](evidencias/crear%20e%20inscribir%20estudiantes.png)

### Desinscribir estudiantes y verificar

![desinscribir](evidencias/desinscribir%20estudiante%20y%20verificar.png)