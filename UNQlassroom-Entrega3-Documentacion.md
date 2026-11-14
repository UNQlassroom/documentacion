# Entrega 3 - UNQlassroom

## Resumen Ejecutivo

### Qué se agregó/modificó en esta iteración

En esta tercera entrega, la plataforma UNQlassroom alcanza su madurez funcional integrando herramientas avanzadas de automatización, control de calidad de código, identidad extendida de usuarios y gestión integral del ciclo de vida académico de los cursos y asignaciones.

Las funcionalidades desarrolladas en esta iteración incluyen:

- **Identidad Extendida y Preferencia de Usuario**, incorporando la visualización y configuración de nombres y apellidos reales junto al usuario de GitHub, un menú desplegable de cuenta centralizado y modales de bienvenida y actualización de datos personales.
- **Gestión Académica Avanzada**, permitiendo la baja lógica o administrativa de alumnos inscriptos y el archivado de cursos finalizados con su correspondiente sincronización y bloqueo en GitHub.
- **Automatización y Estandarización de Templates**, permitiendo la importación avanzada de repositorios externos (propios o de terceros mediante slugs o URLs) hacia la organización de la facultad, aplicando de forma automatizada la detección del stack tecnológico (Java, Python, Node.js, C#/.NET), inyección de herramientas de calidad (JaCoCo, Checkstyle, Ruff, ESLint, etc.), parsers, wrappers y caché optimizada por commit SHA.
- **Diagnóstico y Alertas Pedagógicas en Tiempo Real**, integrando procesamiento de webhooks para métricas de commits atómicos, diagnóstico detallado de fallas de Integración Continua (CI/CD) con visualización de logs y bloques de código, análisis estático de linters con contadores y listas colapsables, y métricas de cobertura de código (JaCoCo/JUnit) con widgets de progreso y semáforos de cumplimiento.
- **Mejoras de Experiencia e Interfaz (UX/UI)**, rediseñando el panel de asignaciones mediante menús contextuales en cascada (Kebab Menu), vistas detalladas dedicadas por asignación, barras de estado técnico integradas, limpieza visual de badges redundantes y tooltips interactivos de asistencia sobre Git.

### Decisiones tomadas

#### Sincronización en tiempo real mediante Server-Sent Events (SSE)
Para lograr una actualización fluida y transparente en el frontend sin recurrir a sondeos periódicos (polling) innecesarios ni a la complejidad de WebSockets bidireccionales, se implementó una infraestructura de streaming unidireccional utilizando SseEmitter en Spring MVC del lado del backend y fetch-event-source autenticado con tokens Bearer en el cliente React. Esto permite que cualquier cambio procesado en los webhooks de GitHub sea notificado instantáneamente a los usuarios suscritos mediante un canal global y seguro.

#### Consolidación de identidades y trazabilidad de nombres reales
Dado que GitHub actúa exclusivamente como proveedor de autenticación y repositorio de código mediante *usernames*, se estructuró un subsistema local de perfiles que vincula de manera transparente el nombre y apellido real del usuario. Esto permite que las vistas colaborativas, listados de inscriptos, paneles de corrección y buscadores operen de forma inclusiva y natural, filtrando indistintamente por nombre completo o por identificador técnico sin perder la trazabilidad con los commits.

---

# User Stories

## US92 - Eliminar Alumnos

### Actor/es
- Docente

### Funcionalidad
Como docente quiero dar de baja a estudiantes de mi curso para evitar generarles asignaciones o repositorios innecesarios a personas que abandonaron la cursada.

### Valor aportado
Optimiza la administración del curso y previene la saturación de recursos en la organización de GitHub al desvincular tempranamente a estudiantes que no continuarán con las actividades académicas.

### Criterios de aceptación
- Debe existir un botón de "Eliminar alumno" que esté ubicado junto a cada estudiante en el listado de alumnos inscriptos del curso, visible únicamente para el docente a cargo.
- Debe mostrar un modal de confirmación al presionar el botón de eliminar que especifique el nombre y usuario de GitHub del alumno a dar de baja, advirtiendo que perderá el acceso al curso y a sus actividades, con opciones para "Confirmar" y "Cancelar".
- Debe eliminar la inscripción del alumno del curso al confirmarse la acción, garantizando que el estudiante ya no aparezca en el listado del curso y que el curso deje de figurar en el panel principal del alumno.
- Debe mostrar una notificación o mensaje de éxito confirmando la baja del alumno.

---

## US139 - Visualización de nombre real

### Actor/es
- Usuario (Alumno / Docente)

### Funcionalidad
Como usuario quiero ver el nombre y apellido real de las personas junto a su usuario de GitHub en las distintas pantallas de la web.

### Valor aportado
Permite reconocer inmediatamente a los alumnos y compañeros en listas de inscriptos, grupos y correcciones sin depender exclusivamente de pseudónimos técnicos o usernames de GitHub.

### Criterios de aceptación
- Donde aparezca un usuario, se debe mostrar su nombre y apellido de forma destacada (ej: en negrita) y, debajo o al lado en un tono más tenue/secundario, su usuario de GitHub precedido por un arroba (ej: Juan Pérez @Juanperez99).
- Si el usuario no tiene nombre completo registrado o está vacío, debe mostrarse únicamente su @username de GitHub.
- Cada buscador que antes funcionaba por usernames de GitHub debe permitir filtrar indistintamente tanto por nombre/apellido como por usernames de GitHub.

---

## US166 - Menú desplegable de usuario y opciones de cuenta

### Actor/es
- Usuario (Alumno / Docente)

### Funcionalidad
Como usuario quiero acceder a un menú desplegable desde mi avatar en la barra de navegación para visualizar la información de mi cuenta, acceder a mis opciones personales y cerrar sesión de forma rápida.

### Valor aportado
Centraliza las acciones relativas a la sesión y al perfil en un punto de acceso intuitivo y accesible desde cualquier vista de la plataforma.

### Criterios de aceptación
- Debe existir un botón de acceso en la barra de navegación, ubicado en la esquina superior derecha.
- Debe mostrar la foto de perfil de GitHub, el nombre del usuario y un indicador de menú desplegable.
- Al abrir el menú, debe mostrar el encabezado "Cuenta conectada" con el nombre completo y el nombre de usuario de GitHub.
- Debe contener un botón de "Mis datos" que permita abrir la visualización y edición de la información personal del usuario.
- Si el usuario es docente, el menú debe incluir accesos directos para "Mis Cursos" (vista principal de cursos activos) y "Cursos Archivados".
- Debe incluir la opción "Cerrar sesión" que limpie la sesión activa y redirija al usuario al login.

---

## US138 - Configuración de nombre real

### Actor/es
- Usuario (Alumno / Docente)

### Funcionalidad
Como usuario quiero ingresar o actualizar mi nombre y apellido para que me identifiquen fácilmente en el curso sin confusión.

### Valor aportado
Garantiza la completitud y veracidad de los datos de perfil de la comunidad académica desde el primer acceso a la plataforma.

### Criterios de aceptación
- Al iniciar sesión por primera vez, debe desplegarse un modal de bienvenida que solicite nombre y apellido.
- El usuario debe poder modificar su nombre y apellido clickeando en un botón situado en el menú desplegable del usuario.
- El backend debe validar que el nombre no contenga caracteres inválidos ni exceda un límite de 35 caracteres.

---

## US89 - Archivar Cursos

### Actor/es
- Docente

### Funcionalidad
Como docente quiero poder archivar cursos para que los semestres anteriores ya no figuren en mi pantalla inicial.

### Valor aportado
Mantiene limpia la interfaz operativa del profesor a medida que transcurren los ciclos lectivos, resguardando el historial y pasando los repositorios a modo de solo lectura.

### Criterios de aceptación
- Debe existir un botón "Archivar", o el mismo debe archivarse luego de 2 meses de finalización del semestre.
- Los repositorios generados en el curso deben ser archivados en GitHub.
- Debe mostrar un modal de confirmación al presionar el botón "Archivar" que detalle que los repositorios pasarán a solo lectura y el curso dejará de verse en la pantalla principal.
- Debe existir una sección de "Cursos Archivados" que permita al docente acceder y consultar el historial de cursos anteriores cuando lo desee.

---

## US134 - Asistencia con Tooltips

### Actor/es
- Alumno

### Funcionalidad
Como alumno quiero tener acceso a tooltips sobre resolución de conflictos y comandos comunes de Git.

### Valor aportado
Actúa como una herramienta pedagógica de soporte autónomo, permitiendo al estudiante destrabarse ante errores comunes de sincronización con el repositorio remoto.

### Criterios de aceptación
- Debe incluirse un componente de ayuda flotante o pestañas de "Guía Git" dentro de la vista de la asignación.
- El mismo debe contener tooltips interactivos para entender cómo resolver conflictos, revertir commits, sincronizar ramas, y disparar a demanda un pipeline de CI.

---

## US151 - Estandarización automática de repositorios template

### Actor/es
- Docente

### Funcionalidad
Como docente quiero que los repositorios de los trabajos prácticos de mis alumnos incluyan automáticamente la configuración completa de CI, JaCoCo y Linter al crearse, optimizando las validaciones mediante caché.

### Valor aportado
Elimina la configuración manual repetitiva por parte del docente, asegurando estándares rigurosos de calidad de código desde el primer commit en todas las entregas.

### Criterios de aceptación
- El sistema debe implementar un mecanismo de caché en base de datos para los templates de los profesores utilizando el último commit conocido (`commit_sha`) y un estado de aprobación.
- Si el template ya fue procesado y su commit no cambió, el sistema debe aplicar la estandarización de forma directa y optimizada sin re-escanear archivos.
- Si el template es nuevo o fue actualizado, el backend debe inspeccionar el gestor de dependencias (`pom.xml` o `build.gradle`) e incorporar de forma no destructiva las herramientas de calidad (JaCoCo y Linter) y wrappers si faltasen.
- El sistema debe inyectar obligatoriamente el archivo `.github/workflows/ci.yml` estandarizado (con compilación, tests, linter, cálculo de líneas y POST al backend), pisando cualquier versión anterior si correspondiera.
- Todo el proceso debe ejecutarse de manera automática e invisible al momento de crear el repositorio.

---

## US131 - Alertas y diagnóstico de Calidad de Git

### Actor/es
- Alumno

### Funcionalidad
Como alumno quiero recibir advertencias y recomendaciones cuando mis commits contengan demasiados cambios o mensajes ambiguos.

### Valor aportado
Fomenta buenas prácticas de desarrollo de software profesional, guiando al alumno en la realización de commits atómicos y el uso de mensajes descriptivos.

### Criterios de aceptación
- El backend debe calcular métricas del commit (cantidad de líneas modificadas, archivos tocados y mensaje del commit).
- Si un commit supera un umbral configurable (ej: >15-20 archivos o >500 líneas en un solo commit que no sea init/merge), el sistema debe marcar una advertencia de "Commit No Atómico detectado".
- En la pantalla de la asignación, se debe desplegar una tarjeta/badge de alerta pedagógica explicando la recomendación de partir los cambios.
- Si el mensaje de commit no sigue buenas prácticas (ej: "fix", "cambios", "asd", mensajes vacíos o menores a 5 caracteres), se debe sugerir el formato Conventional Commits.
- Las advertencias no bloquean la entrega, sirviendo como guía formativa visible tanto para el alumno como para el docente.

---

## US132 - Alertas y diagnóstico de errores de CI

### Actor/es
- Alumno

### Funcionalidad
Como alumno quiero ver el diagnóstico detallado de las fallas de CI/CD directamente en la vista de mi asignación.

### Valor aportado
Reduce el tiempo de depuración ante errores de compilación o pruebas, evitando la navegación por registros complejos en la interfaz nativa de GitHub Actions.

### Criterios de aceptación
- Al recibir un webhook de `workflow_run` con estado `failure`, el backend debe consultar la API de GitHub Actions para extraer la etapa específica que falló (ej: `mvn compile`, `mvn test`, `npm run build`).
- Se debe almacenar o computar un resumen legible del error (step fallido, mensaje de error principal o stack trace acotado).
- En el frontend, se debe proveer un botón/acordeón "Ver detalle del error" que muestre la etapa fallida, un extracto del log formateado en bloque de código con sintaxis oscura y opción de copiar, junto con un enlace directo a la corrida en GitHub Actions.

---

## US135 - Alertas y diagnóstico de Linter

### Actor/es
- Alumno

### Funcionalidad
Como alumno quiero visualizar los avisos del linter sobre mi código para corregir estilos y code smells.

### Valor aportado
Facilita la adherencia temprana a los estándares de estilo de la materia mediante reportes de análisis estático estructurados y claros.

### Criterios de aceptación
- El sistema debe recibir de forma automatizada (vía webhook/POST del CI) el reporte de análisis estático del linter ejecutado en el repositorio del alumno.
- El backend debe procesar y almacenar las incidencias detectadas (errores, advertencias, archivo, línea y regla violada).
- El frontend debe mostrar un contador de incidencias (errores y advertencias) junto con una lista colapsable que desglose archivo, línea y regla violada.

---

## US133 - Métricas de cobertura de código y estado de tests (JaCoCo / JUnit)

### Actor/es
- Alumno

### Funcionalidad
Como alumno quiero visualizar la cobertura de código y el reporte de tests unitarios de mi proyecto.

### Valor aportado
Brinda métricas objetivas sobre el nivel de prueba del software desarrollado, asegurando transparencia en la verificación de requisitos de testing.

### Criterios de aceptación
- El sistema debe recibir y procesar de forma automatizada (vía webhook/POST del CI) el reporte de cobertura de líneas de JaCoCo y los resultados de ejecución de tests de JUnit.
- El backend debe almacenar el resumen consolidado: porcentaje de cobertura de líneas y cantidad de tests ejecutados, pasados, fallados o salteados.
- En la interfaz del alumno: widget con barra de progreso que refleje el porcentaje de cobertura de líneas, indicador visual/semáforo respecto al umbral mínimo sugerido por la cátedra (ej: >80%), y resumen visual del estado de los tests unitarios.

---

## US129 - Visualizar asignación en detalle

### Actor/es
- Alumno

### Funcionalidad
Como alumno del curso quiero acceder a una vista dedicada y detallada de mi asignación para ver con claridad el estado de mi repositorio y entregas.

### Valor aportado
Organiza la información técnica y académica de manera enfocada, proveyendo un centro de comando único por cada tarea asignada.

### Criterios de aceptación
- Al hacer click en una asignación desde el panel general, el alumno debe ser redirigido a una página específica: `/cursos/:cursoId/asignaciones/:asignacionId`.
- La vista debe mostrar título, descripción, fecha límite, plantilla utilizada, estado de entrega (Entregado, Pendiente, Calificado) y enlace directo al repositorio.
- Se debe presentar un panel de estado técnico con accesos a estado de CI/CD, cobertura y métricas, alertas de buenas prácticas en Git, y tooltips. Si la asignación es grupal, debe listar a los miembros del equipo y sus respectivos roles.

---

## US130 - Limpieza visual del panel de asignaciones

### Actor/es
- Docente

### Funcionalidad
Como docente quiero tener una interfaz más limpia, despejada y ordenada en la pestaña de asignaciones para enfocarme en la información relevante de cada tarea.

### Valor aportado
Reduce la fatiga visual y agiliza las acciones de administración cotidiana mediante menús contextuales simplificados.

### Criterios de aceptación
- Se debe remover el indicador/badge de estado de CI de la pestaña principal de "Asignaciones" del docente, ya que dicha información se visualiza en profundidad en la sección de "Métricas de repositorios".
- Se debe implementar un Menú Kebab (tres puntos verticales ⋮) para agrupar las acciones de editar o eliminar asignación, evitando botones redundantes en cada tarjeta.
- Las modales asociadas deben contener texto más liviano y directo.

---

## US163 - Importación y Estandarización de Repositorios Template desde GitHub

### Actor/es
- Docente

### Funcionalidad
Como docente de la plataforma UNQlassroom, quiero poder importar un repositorio existente de GitHub (propio o de terceros mediante su URL) para transformarlo en una plantilla oficial de la cátedra.

### Valor aportado
Permite reutilizar código base y consignas previas de manera automatizada, aplicando estandarización tecnológica sin fricciones manuales.

### Criterios de aceptación
- **Endpoint de importación:** El sistema expone un `POST /templates` protegido para docentes que procesa el parámetro `repoAImportar` (con soporte para URLs HTTPS).
- **Replicación atómica y detección de stack:** La subida de archivos del repo externo se realiza en un único commit atómico utilizando la Git Data API, mientras el backend inspecciona el árbol de archivos para detectar el ecosistema (`JAVA_MAVEN`, `JAVA_GRADLE`, `PYTHON`, `NODE_JS`, `DOTNET`) e inyectar el pipeline correspondiente.
- **Experiencia de usuario (UI):** El front permite ingresar la URL de origen con autocompletado del nombre sugerido, bloqueando controles y mostrando un spinner contextual durante el procesamiento (con timeout HTTP extendido a 60s).
- **Manejo de errores y Sincronización:** Ante fallos (repositorios privados o inválidos), la modal retiene los datos y expone el error claramente. Tras el éxito (`201`), muestra el enlace directo al repositorio creado en GitHub y refresca el listado local de plantillas.
