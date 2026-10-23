# Entrega 2 - UNQlassroom

## Resumen Ejecutivo

### Qué se agregó/modificó en esta iteración

En esta segunda entrega, el sistema profundiza en la automatización y la experiencia de usuario tanto para el docente como para los alumnos, incorporando capacidades de gestión avanzada sobre las asignaciones y sincronización asincrónica bidireccional en tiempo real con la API de GitHub.

Las funcionalidades desarrolladas incluyen:

- **Gestión Avanzada de Asignaciones**, permitiendo al docente modificar los datos permitidos (descripción y fecha de entrega) de una asignación existente con actualización inmediata en tiempo real, o eliminarla de forma segura garantizando la baja de los repositorios asociados en cascada.
- **Administración de Repositorios Template**, facilitando la creación y el listado de plantillas base para estructurar de forma estandarizada las nuevas asignaciones.
- **Sincronización en Tiempo Real con Webhooks de GitHub**, eliminando la necesidad de refrescar manualmente contra la API. Esto incluye la actualización automática del estado de invitación de los usuarios, los commits y estados de CI, la recepción de entregas mediante Releases y el seguimiento de Issues y comentarios en vivo.

### Decisiones tomadas

#### Sincronización asincrónica basada en Webhooks
Para cumplir con los requerimientos de tiempo real sin saturar los límites de peticiones (*rate limits*) de la API de GitHub, se implementó un sistema de recepción de Webhooks. De esta forma, cualquier evento relevante (commits, cambios en issues, aperturas de PRs/Releases o aceptación de invitaciones) impacta de forma inmediata en la base de datos local y refresca la interfaz mediante componentes reactivos, evitando consultas manuales constantes (*polling*).

#### Borrado en cascada asincrónico y validaciones de integridad
Al momento de eliminar una asignación, se priorizó la respuesta inmediata de la interfaz delegando el borrado masivo de los repositorios en la organización de GitHub a un proceso en segundo plano. Asimismo, se implementó una regla de negocio estricta que bloquea la eliminación si la asignación ya posee entregas o calificaciones registradas, resguardando la integridad académica del curso.

---

# User Stories

## US84 - Gestionar repositorios template

### Actor/es
- Docente

### Funcionalidad
Como docente quiero crear y listar repositorios template para poder crear asignaciones en base a ellos.

### Valor aportado
Estandariza el punto de partida para los trabajos prácticos, asegurando que todos los alumnos arranquen con la misma estructura de código base y configuración inicial sin configuraciones manuales propensas a errores.

### Criterios de aceptación
- Debe existir una sección de gestión de templates accesible desde el panel del docente.
- Debo poder visualizar el listado de repositorios template disponibles en la organización.
- Debe permitir la creación de nuevos templates vinculados a la plataforma.

---

## US91 - Modificar Asignaciones

### Actor/es
- Docente

### Funcionalidad
Como docente quiero modificar las asignaciones para enmendar errores.

### Valor aportado
Otorga flexibilidad operativa para corregir fechas límite o descripciones imprecisas sobre la marcha, sin necesidad de dar de baja la tarea y recrearla desde cero.

### Criterios de aceptación
- Deben poder ser modificados los campos de DESCRIPCIÓN y FECHA DE ENTREGA.
- Debe existir un botón "Editar" que esté disponible dentro de la vista de detalle de la asignación.
- Debe precargar un modal con los datos actuales de la asignación al presionar "Editar", permitiendo al docente ver la información previa antes de modificarla.
- Debe mantener bloqueados (en modo solo lectura) los campos de Título, Tipo (individual/grupal) y Repositorio Template.
- Debe actualizar y reflejar los cambios inmediatamente tanto en la vista del docente como en la de los alumnos inscriptos una vez guardados.
- Debe mostrar un mensaje o notificación de éxito confirmando que la asignación fue modificada correctamente y cerrar el modal/formulario.

---

## US90 - Eliminar Asignaciones

### Actor/es
- Docente

### Funcionalidad
Como docente quiero eliminar asignaciones para descartar las creadas por error o canceladas, manteniendo el curso organizado y evitando confusiones a los alumnos.

### Valor aportado
Permite mantener el espacio de trabajo limpio y ordenado, removiendo tareas obsoletas y limpiando de forma automática los recursos asociados en GitHub.

### Criterios de aceptación
- Debe existir un botón "Eliminar", dentro de la vista de detalle de la asignación.
- Debe mostrar un mensaje o modal de confirmación al presionar el botón de eliminar, solicitando que el docente confirme la acción y advirtiendo que la operación es irreversible.
- Debe eliminar del sistema la asignación y disparar la eliminación de todos los repositorios asociados en GitHub de forma asincrónica al ser confirmada, garantizando que la interfaz responda de inmediato y dejen de ser visibles para el docente y los alumnos.
- Debe impedir la eliminación en caso de que la asignación ya cuente con entregas o calificaciones registradas, mostrando un mensaje que explique el motivo.
- Debe notificar al docente con un mensaje de éxito indicando que la asignación fue eliminada correctamente y redirigirlo al listado principal de asignaciones del curso.

---

## US101 - Sincronización en tiempo real de aceptaciones de invitación a la organización

### Actor/es
- Docente

### Funcionalidad
Como docente quiero que el estado de los alumnos invitados se actualice automáticamente cuando aceptan la invitación para saber quiénes ya pueden acceder a los repositorios sin verificar a mano en GitHub.

### Valor aportado
Optimiza el seguimiento administrativo del curso, aportando visibilidad instantánea sobre el estado de incorporación de los estudiantes a la organización de GitHub.

### Criterios de aceptación
- El estado del usuario se sincroniza automáticamente sin tener que presionar el botón "Sincronizar con GitHub".

---

## US104 - Sincronización en tiempo real de commits y estado de CI

### Actor/es
- Docente

### Funcionalidad
Como docente quiero que el estado de último commit, fechaÚltimoCommit, y estado de CI se actualice automáticamente cuando un alumno hace alguna de estas acciones, para poder monitorear la actividad de los alumnos en tiempo real sin refrescar la página contra GitHub.

### Valor aportado
Garantiza un monitoreo preciso y al instante de la actividad de desarrollo, facilitando la detección temprana de bloqueos técnicos o falta de avance en los repositorios.

### Criterios de aceptación
- Los cambios en los commits y el pipeline de Integración Continua se reflejan de manera automática en el dashboard del docente apenas ocurren en el repositorio remoto.

---

## US107 - Sincronización en tiempo real de entregas de asignaciones

### Actor/es
- Docente

### Funcionalidad
Como docente quiero que las releases/entregas de los alumnos se actualicen automáticamente cuando un alumno entrega una asignación para poder visualizar en tiempo real quiénes entregaron a término y acceder a la versión final del código para su corrección.

### Valor aportado
Elimina la fricción en la fiscalización de las entregas, notificando de inmediato cuando un estudiante oficializa su versión a evaluar.

### Criterios de aceptación
- La recepción de un nuevo Release en GitHub actualiza instantáneamente el estatus de la entrega en la plataforma del docente.

---

## US108 - Sincronización en tiempo real de correcciones/issues

### Actor/es
- Docente

### Funcionalidad
Como docente quiero ver las actualizaciones en los issues de los repositorios reflejados en tiempo real para dar seguimiento a las consultas, dudas y respuestas de los alumnos sobre sus devoluciones sin tener que presionar el botón "Actualizar correcciones".

### Valor aportado
Agiliza el canal de tutoría y feedback técnico, centralizando la conversación y el estado de los hilos de corrección de forma fluida.

### Criterios de aceptación
- Deben actualizarse al crearse, cerrarse, reabrise o comentarse un issue perteneciente a los repositorios de las asignaciones.
- Debe actualizar el estado y la cantidad de comentarios del issue en la vista de correcciones del docente sin requerir una consulta manual a la API de GitHub.