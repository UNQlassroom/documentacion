# Entrega 1 - UNQlassroom

## Resumen Ejecutivo

### Qué se agregó/modificó en esta iteración

En esta primera entrega, el sistema incorporó los flujos principales para el dictado y cursada de una materia, conectando la plataforma web con la automatización en GitHub.

Las funcionalidades desarrolladas incluyen:

- **Autenticación e Identidad**, integrando registro e inicio de sesión directamente con GitHub para los roles de Alumno y Docente.
- **Gestión de Cursos y Asignaciones**, brindando capacidad para que el docente visualice a sus alumnos y cree asignaciones (individuales o grupales), generando de forma automática los repositorios correspondientes.
- **Flujo de Entregas y Reentregas Automático**, permitiendo al alumno visualizar sus tareas y marcar una asignación como entregada (o reentregarla), lo cual orquesta la creación automática de un Release en su repositorio de GitHub.
- **Sistema de Calificación y Feedback**, incorporando un panel para que el docente asigne notas numéricas locales y accesos directos para generar Issues (correcciones) directamente en el repositorio de cada alumno.

### Decisiones tomadas

#### Autenticación delegada (OAuth con GitHub)
Se decidió no implementar un sistema de contraseñas propio. Al delegar el registro e inicio de sesión a GitHub, nos aseguramos de que la identidad del usuario en la plataforma coincida exactamente con su cuenta real, eliminando errores tipográficos al invitar alumnos y crear repositorios.

#### Entregas inmutables mediante Releases
Para evitar que el alumno lidie con comandos de Git (`git tag`, `git push`) al momento de entregar, el sistema se encarga de generar automáticamente un Release a partir de la rama `main` cuando se acciona el botón "Entregar" o "Reentregar". Esto le asegura al docente una "foto" exacta del código al momento del cierre.

#### Integración ligera para retroalimentación
En lugar de replicar toda la interfaz de comentarios de GitHub dentro de nuestra plataforma, se optó por una estrategia de redirección nativa. El docente califica numéricamente en UNQlassroom, pero al querer dejar comentarios extensos, el sistema lo redirige a la sección de Issues del repositorio específico, aprovechando las herramientas que GitHub ya provee.

---

# User Stories

## US48 - Crear registro/login de usuarios

### Actor/es
- Usuario (Alumno / Docente)

### Funcionalidad
Como usuario quiero registrarme en la aplicación para poder gestionar cursos o acceder a ellos.

### Valor aportado
Simplifica el acceso a la plataforma delegando la seguridad a un proveedor robusto (GitHub) y garantiza la consistencia de los nombres de usuario necesarios para la gestión automatizada de repositorios.

### Criterios de aceptación
- En la pantalla de registro debo ver un mensaje: "Bienvenido a UNQlassroom. Inicia sesión o regístrate utilizando tu cuenta de GitHub seleccionando tu rol."
- En la pantalla de registro debe haber dos botones de "Ingresar como alumno" e "Ingresar como docente".
- Debo poder loguearme con mis credenciales de GitHub.

---

## US49 - Acceder a un curso como docente

### Actor/es
- Docente

### Funcionalidad
Como docente quiero poder acceder a un curso en específico para poder crear asignaciones y tener más detalles del mismo.

### Valor aportado
Centraliza la administración académica en un solo panel de control, permitiendo al profesor gestionar el ciclo de vida completo de su materia y hacer seguimiento del progreso técnico de la clase.

### Criterios de aceptación
- Debo poder ver el listado de alumnos, invitarlos, crear asignaciones individuales/grupales, y ver un resumen de estadísticas de los repositorios de GitHub.
- Debo poder ver el listado de las asignaciones creadas y, dentro de cada una de ellas, el listado de alumno/s asignado/s, junto con los repositorios creados.

---

## US50 - Acceder a un curso como alumno

### Actor/es
- Alumno

### Funcionalidad
Como alumno quiero poder acceder a un curso en específico para poder ver mis asignaciones, mis calificaciones y mis tiempos de entrega asignados.

### Valor aportado
Le otorga al estudiante un entorno claro y organizado para entender qué se espera de él, cuáles son las fechas límite y acceder a las devoluciones del docente sin fricción.

### Criterios de aceptación
- Debo poder ver el listado de asignaciones, junto con mis calificaciones, los estados de entrega y redirección a los issues de cada asignación.

---

## US20 - Crear asignación (Individual/Grupal)

### Actor/es
- Docente

### Funcionalidad
Como docente, quiero crear una nueva asignación para el curso y definir si su resolución será de carácter individual o grupal.

### Valor aportado
Provee la flexibilidad necesaria para estructurar correctamente las diferentes modalidades de evaluación de la cátedra, disparando la creación automática de repositorios adecuados según la modalidad elegida.

### Criterios de aceptación
- La vista específica de un curso debe poseer un botón de "CREAR ASIGNACIÓN" que abra un modal de creación.
- Al completar los datos básicos (título, descripción), se debe visualizar un control para seleccionar la modalidad: "Individual" o "Grupal".
- Si se selecciona "Grupal", la interfaz debe desplegar un componente para seleccionar alumnos inscritos y agruparlos bajo un nombre de equipo.
- Un alumno no debe estar en más de un equipo de una misma asignación.
- Cada asignación debe crear un repositorio: uno por alumno (si es individual) o uno por grupo (si es grupal).

---

## US21 - Visualizar detalles y equipo de la asignación

### Actor/es
- Alumno

### Funcionalidad
Como alumno, quiero ver los detalles de mi asignación activa y la lista de mis compañeros de equipo (si aplica).

### Valor aportado
Garantiza la transparencia en los trabajos colaborativos, permitiendo al estudiante confirmar quiénes tienen acceso al repositorio compartido y entender el contexto general de la entrega.

### Criterios de aceptación
- Al ingresar a la vista principal del curso, se deben visualizar las tarjetas o secciones de las asignaciones activas.
- Si la asignación es "Grupal", debe existir una sección claramente identificada que liste los nombres de todos los compañeros de equipo.
- Si la asignación es "Individual", la sección de compañeros de equipo no debe mostrarse.

---

## US77 - Entregar asignación

### Actor/es
- Alumno

### Funcionalidad
Como usuario quiero entregar una asignación para cumplir a tiempo con mis tareas universitarias.

### Valor aportado
Abstrae la complejidad de etiquetar versiones en Git. El sistema garantiza que la entrega quede formalizada en un punto exacto en el tiempo mediante un Release automático, protegiendo el trabajo del alumno.

### Criterios de aceptación
- Debe existir un botón "entregar asignación" en cada asignación vinculada a mí o a mi equipo.
- Al presionar el botón, debe quedar marcado como "Entregada" y generar un release de MAIN en GitHub.

---

## US82 - Reentregar asignación

### Actor/es
- Alumno

### Funcionalidad
Como alumno quiero reentregar una asignación para corregir errores.

### Valor aportado
Brinda flexibilidad frente a errores de último minuto, permitiendo actualizar la versión a evaluar siempre y cuando los plazos lo permitan, manteniendo el historial limpio.

### Criterios de aceptación
- Debe existir un botón "REENTREGAR" en cada asignación.
- El botón debe abrir un modal que pregunte "¿Querés volver a entregar este trabajo? Al confirmar la reentrega, se creará un nuevo release en el repositorio correspondiente, reemplazando la entrega anterior." 
- El modal debe poseer un botón de "Confirmar Reentrega".

---

## US51 - Calificar asignación

### Actor/es
- Docente

### Funcionalidad
Como docente, quiero calificar las asignaciones de los alumnos para que obtengan una retroalimentación de su trabajo.

### Valor aportado
Permite asentar formalmente la nota de evaluación de cada entrega directamente en la plataforma, centralizando el historial académico del curso.

### Criterios de aceptación
- Cada asignación debe poseer un botón "Calificar" el cual abre un modal de calificación.
- El modal de calificación contiene un campo numérico y un campo de texto en el cual el docente puede escribir la retroalimentación.

---

## US53 - Crear issues sobre las asignaciones

### Actor/es
- Docente

### Funcionalidad
Como docente quiero crear issues sobre las asignaciones de los alumnos para enviarles comentarios.

### Valor aportado
Aprovecha las herramientas nativas de GitHub para el seguimiento de feedback, situando las correcciones exactamente donde los alumnos tienen el código, emulando un flujo de revisión de código profesional.

### Criterios de aceptación
- Debe existir una nueva pestaña del docente "Correcciones" en la que se listen todos los repos de las asignaciones.
- Cada asignación debe poseer un botón "Crear Corrección" el cual redirija a la sección de creación de issues de ese repo en GitHub.
- Debe haber un redirect a GitHub para ver todos los issues de un repo en específico.
