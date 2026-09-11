# PoC - UNQlassroom

## Resumen Ejecutivo

### Qué se agregó/modificó en esta iteración

Durante la PoC (Proof of Concept) del proyecto **UNQlassroom** se desarrollaron las funcionalidades mínimas necesarias para validar la viabilidad técnica del producto, reduciendo la incertidumbre respecto a la orquestación automática de repositorios y la integración con la API de GitHub.

En esta etapa se implementaron y validaron:

- **Dashboard del Docente**, permitiendo la creación de cursos de programación y repositorios para los alumnos.
- **Capacidad de monitoreo de repositorios**, para consolidar y visualizar en tiempo real el estado del código y de las ejecuciones de Integración Continua (CI/CD) de cada alumno.
- **Dashboard del Alumno**, permitiendo el acceso a los cursos asignados junto con sus repositorios académicos correspondientes.
- **Automatización de infraestructura en GitHub**, utilizando una GitHub App para aprovisionar repositorios y asignar permisos de lectura/escritura sin intervención manual.

### Decisiones tomadas

#### Desacoplamiento de la lógica de dominio
Se estableció que la base de datos relacional de la aplicación actuará como la única "fuente de la verdad" para definir la estructura de los cursos y alumnos. GitHub se utilizará estrictamente como capa de infraestructura externa, sin intentar replicar el modelo académico en su sistema de permisos.

#### Gestión de accesos híbrida (Colaboradores y Teams)
Para optimizar el uso de la API y mantener un modelo de permisos escalable, se determinó crear un repositorio con acceso de "colaborador externo" para las asignaciones individuales, reservando el uso de "GitHub Teams" únicamente para los futuros trabajos prácticos de modalidad grupal.

### Desafíos técnicos encontrados

- Manejo seguro de credenciales y autenticación asimétrica (JWT firmado con llave `.pem`) contra la API de GitHub para la interacción automatizada desde el backend.
- Definición de una arquitectura de permisos plana en la organización de GitHub para evitar la herencia no deseada en los repositorios de los estudiantes.

---

# User Stories

## US19 - Monitorear estado de repositorios

### Actor/es
- Docente

### Funcionalidad
Como docente, quiero que la tabla de grupos muestre el último commit, su fecha y el estado del último run del CI del repositorio de cada alumno, para hacer seguimiento del progreso sin tener que entrar a la página de GitHub.

### Valor aportado
Centraliza el seguimiento técnico del aula, proveyendo métricas de desarrollo clave en un solo dashboard y mejorando la capacidad del docente para identificar retrasos en las entregas.

### Criterios de aceptación
- La tabla de grupos/alumnos debe incluir columnas específicas para: nombre del último commit, fecha de ese commit, y el estado del pipeline de CI (ej: ícono verde de éxito o rojo de fallo).

---

## US44 - Visualizar cursos

### Actor/es
- Alumno

### Funcionalidad
Como alumno, quiero visualizar mis cursos para poder acceder a mis repositorios y realizar mis tareas.

### Valor aportado
Centraliza el acceso del estudiante a sus espacios de trabajo académicos, proveyendo contexto inmediato sobre su cursada y reduciendo la dificultad para encontrar su repositorio.

### Criterios de aceptación
- Debo poder ver todos los cursos a los que estoy asignado.
- Debo poder acceder a mi repositorio individual asignado a cada curso.
- Debo poder ver la información de cada curso: materia, año, comisión, y semestre.

---

## US22 - Acceder al repositorio de código

### Actor/es
- Alumno

### Funcionalidad
Como alumno, quiero tener un enlace o botón de acción directo en la vista de mi asignación que me redirija al repositorio de GitHub correspondiente, para clonar el proyecto y comenzar a codificar rápidamente.

### Valor aportado
Reduce drásticamente la fricción inicial para comenzar a trabajar, asegurando que el estudiante aterrice exactamente en el repositorio que la institución provisionó para su evaluación.

### Criterios de aceptación
- El alumno al visualizar el detalle de su asignación, debe tener la opción de "Ir al Repositorio" (o similar) mediante un botón.
- Si la asignación es individual, cuando el alumno hace clic, debe ser redirigido a su repositorio privado único.
- Si el repositorio aún no ha sido creado o hubiera un fallo de sincronización con la API, entonces el botón debe estar deshabilitado o mostrar un estado de "Creando repositorio".

---

## T10 - Registrar y configurar GitHub App

### Objetivo
Instalar y configurar la aplicación institucional en GitHub para habilitar la autenticación backend y establecer los alcances (scopes) necesarios para la manipulación de la organización.

### Valor aportado
Habilita el canal de comunicación seguro y autenticado que permite toda la orquestación automatizada de la plataforma.

### Resultado esperado
- GitHub App instalada en la organización secundaria correspondiente.
- Descarga y almacenamiento seguro de la llave privada (`.pem`) en el entorno de desarrollo del backend.
- Conexión exitosa y validada desde Spring Boot hacia la API de GitHub verificando la correcta asignación de permisos y lectura de metadatos.
