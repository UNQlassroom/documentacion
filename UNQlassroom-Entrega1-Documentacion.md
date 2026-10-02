# Entrega 1 - UNQlassroom

## Resumen Ejecutivo

### Qué se agregó/modificó en esta iteración
En esta primera entrega, el producto evolucionó de una prueba de concepto (PoC) a una plataforma funcional capaz de gestionar el ciclo de vida completo de una asignación académica. 

Las principales funcionalidades incorporadas incluyen:
- **Autenticación segura:** Se implementó el registro y login delegando la identidad a GitHub (OAuth 2.0).
- **Gestión de Asignaciones:** Los docentes ahora pueden crear asignaciones (individuales o grupales) y los alumnos pueden acceder a un panel dedicado para visualizar sus entregas pendientes.
- **Flujo de Entrega Automatizado:** Se desarrolló un sistema donde el alumno entrega su trabajo desde la plataforma, orquestando la creación automática de un *Release* en GitHub.
- **Sistema de Calificación y Retroalimentación Nativo:** Los docentes pueden calificar con una nota numérica y enviar comentarios que impactan directamente en la vista de los alumnos.

### Decisiones tomadas
- **Adopción de OAuth 2.0 para Login:** Se decidió descartar el registro manual con contraseña. Al forzar el inicio de sesión con GitHub, eliminamos el margen de error humano al tipear el *username*, garantizando que la orquestación de repositorios siempre apunte al usuario correcto.
- **Reemplazo de GitHub Teams por manejo propio:** Decidimos abandonar la dependencia de los *Teams* de GitHub para organizar a los alumnos. La base de datos relacional (PostgreSQL) se consolidó como la única fuente de verdad para los roles (Docente/Alumno) y agrupaciones, utilizando a GitHub puramente como infraestructura de almacenamiento de código.
- **Entregas mediante Releases Automáticos:** Para evitar que alumnos sin experiencia lidien con comandos complejos de Git para "sellar" una entrega, decidimos que el backend de Spring Boot automatice la creación de un *Release* en el repositorio cuando el alumno presiona el botón "Entregar" antes de la fecha límite.

---

## User Stories

### US48 - Crear registro/login de usuarios[cite: 1]
**Actor/es:** Docente / Alumno
**Funcionalidad:** Como usuario, quiero poder registrarme e iniciar sesión en la plataforma utilizando mi cuenta de GitHub.

**Valor aportado:** Agiliza el acceso a la plataforma, elimina la necesidad de gestionar contraseñas locales y garantiza que el nombre de usuario coincida exactamente con el perfil de GitHub, evitando fallos en la asignación de repositorios.

**Criterios de aceptación:**
- La pantalla de inicio debe mostrar un botón "Iniciar sesión con GitHub".
- El sistema debe utilizar el protocolo OAuth 2.0 para autenticar al usuario.
- Si el usuario no existe en la base de datos, debe crearlo automáticamente con los datos provistos por GitHub; si existe, debe loguearlo.

---

### US20 - Crear asignación (Individual/Grupal)[cite: 1]
**Actor/es:** Docente
**Funcionalidad:** Como docente, quiero poder crear nuevas asignaciones dentro de mi curso, definiendo si son de resolución individual o grupal.

**Valor aportado:** Permite estructurar la cursada, definir las reglas de entrega y preparar el terreno para que el sistema aprovisione los repositorios correspondientes para cada alumno o equipo.

**Criterios de aceptación:**
- Debe existir un formulario de creación de asignación dentro de la vista del curso.
- El formulario debe permitir ingresar título, descripción, fecha límite (*deadline*) y seleccionar la modalidad (Individual o Grupal).
- Al guardar, la asignación debe quedar listada en el panel del curso.

---

### US21 - Visualizar detalles y equipo de la asignación[cite: 1]
**Actor/es:** Docente / Alumno
**Funcionalidad:** Como usuario, quiero poder entrar a una asignación específica para ver su descripción, fecha de entrega y quiénes conforman el equipo de trabajo.

**Valor aportado:** Otorga claridad sobre los requisitos del trabajo práctico y fomenta la organización interna de los alumnos al transparentar quiénes tienen acceso al repositorio compartido.

**Criterios de aceptación:**
- Al hacer clic en una asignación, se debe navegar a una vista de detalle.
- Se debe mostrar el título, la consigna, la fecha límite y el estado actual de la entrega.
- Se debe mostrar un listado con los nombres/usuarios de los integrantes asignados a esa tarea.

---

### US49 - Acceder a un curso como docente[cite: 1]
**Actor/es:** Docente
**Funcionalidad:** Como docente, quiero poder acceder a un curso en específico para poder crear asignaciones y tener más detalles del mismo.

**Valor aportado:** Centraliza la administración de la materia, dándole al profesor un panel de control único para gestionar alumnos, crear tareas y monitorear el progreso.

**Criterios de aceptación:**
- Debo poder ver el listado de alumnos e invitarlos.
- Debo poder crear asignaciones individuales/grupales.
- Debo poder ver el listado de las asignaciones creadas y, dentro de cada una, el listado de alumnos asignados junto con los repositorios creados.

---

### US50 - Acceder a un curso como alumno[cite: 1]
**Actor/es:** Alumno
**Funcionalidad:** Como alumno, quiero poder acceder a un curso en específico para poder ver mis asignaciones, mis calificaciones y mis tiempos de entrega asignados.

**Valor aportado:** Le brinda al estudiante un espacio de trabajo organizado, reduciendo la fricción para encontrar sus tareas y consultar el estado de sus evaluaciones.

**Criterios de aceptación:**
- Debo poder ver el listado de asignaciones del curso.
- Debo poder visualizar mis calificaciones y los estados de entrega (pendiente, entregado, corregido).
- Debo disponer de redirección rápida a los *issues* y repositorios de cada asignación.

---

### US77 - Entregar asignación[cite: 1]
**Actor/es:** Alumno
**Funcionalidad:** Como alumno, quiero poder marcar mi asignación como entregada desde la plataforma para que el docente sepa que finalicé mi trabajo.

**Valor aportado:** Facilita el proceso de entrega sin requerir conocimientos avanzados de control de versiones. Automatiza la generación de una versión inmutable (*Release*) del código para que el docente corrija exactamente lo que se entregó antes del cierre.

**Criterios de aceptación:**
- La vista de la asignación debe tener un botón para "Entregar" que solo esté habilitado si la fecha actual es anterior al *deadline*.
- Al accionar el botón, el sistema debe cambiar el estado de la entrega en la base de datos.
- El backend debe comunicarse con GitHub para generar automáticamente un *Release* en el repositorio del alumno con el código actual de la rama principal.

---

### US51 - Calificar asignación[cite: 1]
**Actor/es:** Docente
**Funcionalidad:** Como docente, quiero calificar las asignaciones de los alumnos para que obtengan una nota numérica por su trabajo.

**Valor aportado:** Cierra el ciclo de evaluación académica, permitiendo llevar un registro persistente del rendimiento del alumno en la base de datos de la institución.

**Criterios de aceptación:**
- Cada entrega listada en el panel del docente debe poseer un botón "Calificar".
- Al accionar el botón, se debe abrir un modal de calificación.
- El modal debe contener un campo numérico para ingresar la nota, la cual debe guardarse en la base de datos asociada a esa entrega/release.

---

### US53 - Crear issues sobre las asignaciones[cite: 1]
**Actor/es:** Docente
**Funcionalidad:** Como docente, quiero poder escribir una retroalimentación detallada al momento de calificar, y que esta se publique automáticamente como un *Issue* en el repositorio del alumno.

**Valor aportado:** Entrega el *feedback* en el entorno natural del desarrollador (GitHub), fomentando que el alumno interactúe con las herramientas estándar de la industria para leer sus correcciones.

**Criterios de aceptación:**
- El modal de calificación debe incluir un campo de texto para la retroalimentación.
- Al confirmar la calificación, el backend debe consumir la API de GitHub para crear un *Issue* en el repositorio correspondiente.
- El *Issue* creado debe contener el texto del docente y las etiquetas (*labels*) pertinentes (ej. "correccion-docente").
