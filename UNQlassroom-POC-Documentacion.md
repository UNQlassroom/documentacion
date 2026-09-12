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

#### Modelo de persistencia híbrido y delegación en GitHub
Se adoptó un modelo de integración donde GitHub actúa como el registro de membresía, validando y obteniendo la pertenencia de los       
alumnos al curso a través de los GitHub Teams. Asimismo, la creación y gestión de accesos a los repositorios se delega en la organización de    
GitHub, mientras que la base de datos local funciona como soporte relacional, almacenando las referencias y vínculos esenciales entre cursos,   
usuarios y repositorios.

#### Gestión de accesos híbrida (Colaboradores y Teams)
Para optimizar el uso de la API y mantener un modelo de permisos escalable, se determinó crear un repositorio con acceso de "colaborador externo" para las asignaciones individuales, reservando el uso de "GitHub Teams" únicamente para los futuros trabajos prácticos de modalidad grupal.

### Desafíos técnicos encontrados

- Manejo seguro de credenciales y autenticación asimétrica (JWT firmado con llave `.pem`) contra la API de GitHub para la interacción automatizada desde el backend.
- Definición de una arquitectura de permisos plana en la organización de GitHub para evitar la herencia no deseada en los repositorios de los estudiantes.

---

# User Stories

## US2 - Agregar alumno al curso

### Actor/es
- Docente

### Funcionalidad
Como profesor, quiero agregar alumnos a un curso para poder tenerlos listados y asignarles tareas.

### Valor aportado
Facilita la gestión de la matrícula dentro de la plataforma, permitiendo incorporar estudiantes al entorno de evaluación directamente mediante sus usuarios de GitHub.

### Criterios de aceptación
- Debe existir un botón "Invitar" en la vista de cada curso que, al ser accionado, abra una ventana o modal emergente.
- La ventana de invitación debe proveer un campo de texto en el cual el profesor pueda ingresar los nombres de los usuarios de GitHub a añadir al curso.
- Junto al campo de texto, debe existir un botón "Invitar alumnos" que, al ser accionado, envíe la invitación a los usuarios especificados.

---

## US46 - Crear repositorio individual por alumno

### Actor/es
- Docente

### Funcionalidad
Como profesor, quiero que al invitar a un alumno al curso se le genere automáticamente un repositorio individual en GitHub para que pueda comenzar a programar rápidamente.

### Valor aportado
Automatiza la provisión de infraestructura inicial para cada estudiante, eliminando la creación manual de repositorios y garantizando una convención de nombres uniforme en toda la organización.

### Criterios de aceptación
- El repositorio generado en GitHub debe respetar estrictamente el formato `[curso]_[nombreDeUsuarioDelAlumno]` (por ejemplo: `2026s2_c3_programacion_funcional_thiagoDePrueba`).
- El repositorio debe ser aprovisionado exitosamente en la organización incluso si el alumno todavía no es miembro activo de la misma.

---

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
