# Protocolo de Pruebas de Usabilidad — Rallye Motor's

## 2.1 Nombre del proyecto

**Rallye Motor's** — Plataforma web de atención y gestión de servicios

---

## 2.2 Objetivo principal

Evaluar la usabilidad de la plataforma web de Rallye Motor's para garantizar una experiencia eficaz, eficiente y satisfactoria para los usuarios finales, identificando fricciones, errores de navegación y oportunidades de mejora antes de la entrega final del sistema.

---

## 2.3 Metodología

Se llevarán a cabo pruebas presenciales con tres usuarios. Cada participante realizará tareas específicas dentro de la plataforma mientras el equipo observa y registra su comportamiento sin intervenir. Las sesiones serán grabadas en video para análisis posterior en el Sprint 3.

### Justificación del tamaño de la muestra

Se trabaja con tres participantes en coherencia con el enfoque exploratorio de la prueba y las restricciones de tiempo y recursos propias del sprint. La investigación en usabilidad (Nielsen & Landauer) señala que entre 3 y 5 usuarios permiten detectar la mayoría de los problemas de usabilidad críticos en una primera ronda de pruebas. El objetivo de esta fase no es la validación estadística sino la identificación de fricciones prioritarias antes de la entrega final.

### Distribución de tareas por participante

| Hipótesis | Usuario 1 — PO (administrador) | Usuario 2 — Cliente | Usuario 3 — Cliente |
|---|---|---|---|
| H1 — Agendamiento de servicio técnico | ✓ | ✓ | ✓ |
| H2 — Cancelación de cita | ✓ | ✓ | ✓ |
| H3 — Consulta de repuestos | ✓ | ✓ | ✓ |
| H4 — Catálogo y cotización | ✓ | ✓ | ✓ |
| H5 — Panel administrativo | ✓ | — | — |

Los usuarios 2 y 3 realizan las hipótesis 1 a 4, que corresponden al recorrido completo del usuario cliente. El Usuario 1 (Product Owner) realiza las cinco hipótesis: primero el recorrido del cliente para evaluar la experiencia desde esa perspectiva, y luego la hipótesis 5 para evaluar el panel administrativo simulando el rol de administrador del local.

---

## 2.4 Criterios de selección de participantes

Los participantes deben cumplir el siguiente perfil:

| Criterio | Usuario 1 (PO / Administrador) | Usuarios 2 y 3 (Clientes) |
|---|---|---|
| Rol en el sistema | Administrador del local | Usuario cliente |
| Relación con motocicletas | No requerida (perfil de gestión) | Preferiblemente propietario o usuario habitual de motocicleta |
| Experiencia con servicios técnicos | No requerida | Deseable (haber agendado o consultado servicios técnicos previamente) |
| Familiaridad con plataformas digitales | Alta (uso frecuente de herramientas web o aplicaciones de gestión) | Media o alta (uso habitual de aplicaciones web o móviles) |
| Disponibilidad | Acordada con el Product Owner | Acordada en el entorno universitario |

La selección de usuarios 2 y 3 debe priorizar perfiles representativos del usuario objetivo del sistema (clientes reales o potenciales de talleres de motocicletas), evitando participantes del equipo de desarrollo.

---

## 2.5 Logística de la prueba

**Lugar:** Las pruebas se realizarán en dos entornos presenciales:

- **Usuarios 2 y 3:** instalaciones de la universidad, en un espacio habilitado para la sesión.
- **Usuario 1 (Product Owner):** entorno externo a la universidad, en un lugar acordado con el Product Owner, posiblemente un centro comercial o espacio de trabajo cercano.

**Fecha:** A definir al inicio del Sprint 3.

**Duración estimada por sesión:** 30 a 45 minutos.

### Roles del equipo durante la sesión

Cada sesión requiere la participación de tres integrantes del equipo con roles diferenciados:

| Rol | Responsabilidad |
|---|---|
| **Moderador principal** | Conduce la sesión: saluda al participante, explica el propósito de la prueba, lee los escenarios e instrucciones de cada tarea, aplica las preguntas previas y posteriores, y administra el cuestionario SUS. No interviene durante la ejecución de las tareas salvo en los casos definidos en el protocolo de intervención. |
| **Observador de métricas** | Registra con cronómetro el tiempo de inicio y finalización de cada tarea, cuenta y tipifica los errores de navegación según la definición establecida, y completa el formulario de observación en tiempo real. |
| **Observador de comportamiento** | Registra comentarios espontáneos del usuario (pensamiento en voz alta), expresiones no verbales (confusión, frustración, seguridad, satisfacción) y cualquier reacción significativa asociada a elementos específicos de la interfaz. Opera la grabación durante toda la sesión. |

### Equipos

- Computador portátil con la plataforma lista para usar
- Cámara o celular para grabación de la sesión
- Cronómetro para medición de tiempos por tarea
- Formularios de observación impresos o digitales por sesión

---

## 2.6 Definición de error de navegación

Para efectos de este protocolo, se considera **error de navegación** cualquiera de las siguientes situaciones observadas durante la ejecución de una tarea:

| Tipo de error | Descripción |
|---|---|
| **Selección incorrecta** | El usuario selecciona una opción, enlace o botón que no corresponde al flujo esperado para completar la tarea. |
| **Retroceso no planificado** | El usuario usa el botón de retroceso del navegador o navega hacia atrás en el flujo sin haber completado el paso actual. |
| **Pausa prolongada** | El usuario permanece sin interactuar con la interfaz durante más de 10 segundos en un punto que no representa una espera del sistema, indicando desorientación o duda. |
| **Repetición de acción** | El usuario ejecuta la misma acción más de una vez (por ejemplo, hacer clic en el mismo botón repetidamente o rellenar un campo que ya había completado). |
| **Solicitud de ayuda** | El usuario verbaliza explícitamente que no sabe cómo continuar o pregunta al moderador qué debe hacer. |

El observador de métricas debe registrar cada error con su tipo, el momento de la sesión en que ocurrió y el elemento de la interfaz con el que estaba interactuando el usuario.

---

## 2.7 Protocolo de intervención del moderador

El moderador principal **no interviene** durante la ejecución de las tareas salvo en las siguientes condiciones:

1. **Tiempo de espera antes de intervenir:** si el usuario está completamente bloqueado (sin posibilidad de continuar) durante al menos **15 a 20 segundos** después de una pausa prolongada o tras haber verbalizado que no puede continuar.
2. **Tipo de ayuda permitida:** la intervención debe ser mínima y no guiada. El moderador puede hacer preguntas neutras que ayuden al usuario a retomar la exploración sin revelar el paso correcto. Ejemplos permitidos: *"¿Qué esperarías encontrar en esta pantalla?"*, *"¿Qué harías a continuación si estuvieras solo?"*. No se permite señalar elementos de la interfaz ni describir pasos.
3. **Forma estandarizada de intervención:** el moderador debe usar siempre la misma frase para iniciar la intervención: *"Tómate tu tiempo. ¿Qué estás pensando en este momento?"*. Si tras esta pregunta el usuario sigue bloqueado, puede añadir: *"¿Hay algo en la pantalla que te llame la atención?"*
4. **Registro:** el observador de métricas debe registrar cada intervención indicando la hipótesis, el momento aproximado y el motivo del bloqueo.

---

## 2.8 Preparación previa a la sesión

Antes de iniciar cada sesión, el equipo debe verificar:

- Plataforma lista y accesible desde el computador de prueba.
- La grabación está configurada y funcionando antes de que el usuario ingrese al espacio.
- Los formularios de observación (uno por sesión) están disponibles para los dos observadores.
- La base de datos está en estado correcto para que el usuario pueda agendar su propia cita en la tarea 1 y cancelarla en la tarea 2, manteniendo coherencia en el flujo de las pruebas.
- **Para la hipótesis 5:** existe en el sistema una cita con estado *atendida* pre-cargada, sobre la cual el administrador podrá registrar el diagnóstico durante la prueba. Esta condición es necesaria porque el sistema solo permite registrar diagnósticos sobre citas cuya atención ya fue completada.
- **Para la hipótesis 5:** las credenciales del administrador del local (usuario y contraseña) están registradas en la base de datos y deben ser entregadas al Usuario 1 antes de iniciar la tarea. El registro de credenciales es responsabilidad del equipo técnico y no hace parte de la prueba.

---

## 2.9 Flujo de actividades de una sesión

1. **Consentimiento informado:** antes de iniciar cualquier actividad, el moderador presenta al participante el formulario de consentimiento informado. El participante debe leerlo y firmarlo (o aprobarlo digitalmente) para autorizar la grabación de la sesión y el uso de los datos recopilados con fines académicos. Sin este paso, la sesión no puede comenzar.
2. **Adecuación del equipo:** verificar que la plataforma esté funcional y que la grabación esté activa antes de que el usuario interactúe con el sistema.
3. **Contextualización del usuario:** el moderador explica el propósito de la prueba, aclara que se evalúa el sistema y no al usuario, e indica que puede hablar en voz alta mientras navega para expresar lo que piensa (técnica de pensamiento en voz alta).
4. **Ejecución de la sesión:** el usuario realiza las tareas asignadas mientras el moderador observa sin intervenir, salvo que aplique el protocolo de intervención definido en la sección 2.7.
5. **Aplicación de preguntas:** el moderador aplica las preguntas previas antes de cada tarea y las preguntas posteriores una vez el usuario la completa o abandona.
6. **Aplicación del cuestionario SUS** al finalizar todas las tareas.
7. **Aplicación del formulario de post-prueba** para capturar la percepción general del participante.
8. **Agradecimiento al usuario** por su participación en la investigación.

---

## 2.10 Hipótesis, tareas y preguntas

### Hipótesis 1 — Agendamiento de servicio técnico

**Hipótesis:** Si el flujo de agendamiento está estructurado en pasos claros con selección de local, categoría, fecha y hora, los usuarios podrán completar la cita sin errores y sin necesidad de ayuda externa en menos de 2 minutos.

**Preguntas previas:**
- ¿Cómo agenda actualmente un servicio técnico para su motocicleta?
- ¿Qué información esperaría que le pidiera una plataforma para agendar una cita de servicio?

**Tarea 1**

> *Escenario:* Su motocicleta necesita un mantenimiento. Desea agendar un servicio técnico seleccionando el local de su preferencia para el próximo sábado en la mañana.
>
> *Instrucción:* Use la plataforma para agendar el servicio técnico con la información solicitada. Recuerde la placa que ingrese porque la necesitará en la siguiente tarea.

**Preguntas posteriores:**
- ¿Encontró fácilmente la opción para agendar el servicio?
- ¿Hubo algún paso que le resultara confuso o poco claro?
- ¿La información que le pidió el formulario le pareció suficiente y comprensible?

**Criterios de evaluación:**
- Al menos 2 de 3 usuarios completan la tarea sin ayuda del moderador.
- Al menos 2 de 3 usuarios completan la tarea en menos de 2 minutos.
- Ningún usuario comete más de 2 errores de navegación durante el flujo.

---

### Hipótesis 2 — Cancelación de cita

**Hipótesis:** Si el sistema permite cancelar una cita mediante la búsqueda por placa de la motocicleta con un flujo de pocos pasos, los usuarios podrán completar la cancelación de forma autónoma en menos de 90 segundos.

**Preguntas previas:**
- ¿Qué tan fácil espera que sea cancelar una cita que ya agendó?
- ¿Qué información cree que debería pedirle el sistema para encontrar su cita?

**Tarea 2**

> *Escenario:* Agendó un servicio técnico en la tarea anterior pero surgió un imprevisto y necesita cancelarlo.
>
> *Instrucción:* Use la plataforma para encontrar la cita que acaba de agendar usando la placa que ingresó, y cancélela.

**Preguntas posteriores:**
- ¿Le resultó fácil encontrar su cita ingresando la placa?
- ¿El proceso de cancelación fue claro y directo?
- ¿Quedó claro que la cita fue cancelada exitosamente?

**Criterios de evaluación:**
- Al menos 2 de 3 usuarios completan la cancelación sin ayuda.
- Al menos 2 de 3 usuarios completan la tarea en menos de 90 segundos.
- El mensaje de confirmación de cancelación es comprendido por todos los usuarios sin necesidad de explicación adicional.

---

### Hipótesis 3 — Consulta de repuestos

**Hipótesis:** Si el flujo de consulta de repuestos guía al usuario mediante preguntas secuenciales sobre el repuesto, la referencia y el modelo o año de la motocicleta, los usuarios podrán encontrar el repuesto que necesitan sin conocimientos técnicos previos y sin abandonar el flujo a mitad del proceso.

**Preguntas previas:**
- ¿Alguna vez ha buscado un repuesto para su motocicleta? ¿Cómo lo hizo?
- ¿Qué información sobre su moto tiene disponible normalmente cuando necesita un repuesto?

**Tarea 3**

> *Escenario:* Necesita un repuesto para su motocicleta Yamaha y quiere consultar si está disponible en Rallye Motor's.
>
> *Instrucción:* Use la plataforma para consultar si hay repuestos disponibles para su motocicleta Yamaha. El sistema le hará algunas preguntas para ayudarle a encontrar lo que necesita.

> **Nota de moderación:** esta instrucción es intencionalmente abierta. No se mencionan los pasos del flujo para evaluar si el usuario puede descubrirlos de forma autónoma. El moderador no debe anticipar ni detallar las preguntas del sistema.

**Preguntas posteriores:**
- ¿Las preguntas que le hizo el sistema fueron fáciles de responder?
- ¿Los resultados que obtuvo correspondían a lo que buscaba?
- ¿Sintió en algún momento que el flujo era confuso o que no sabía cómo continuar?

**Criterios de evaluación:**
- Al menos 2 de 3 usuarios completan el flujo sin abandonarlo.
- Al menos 2 de 3 usuarios identifican el repuesto buscado en menos de 2 minutos.
- Ningún usuario requiere más de una intervención del moderador para completar la tarea.

---

### Hipótesis 4 — Visualización del catálogo y cotización

**Hipótesis:** Si el catálogo presenta las motocicletas con imagen, referencia y precio de forma organizada y permite filtrar por categoría o precio, los usuarios podrán explorar las opciones disponibles y generar una cotización de forma intuitiva en menos de 3 minutos.

**Preguntas previas:**
- ¿Cómo buscaría normalmente información sobre una motocicleta antes de comprarla?
- ¿Qué información esperaría ver en un catálogo de motos en línea?

**Tarea 4**

> *Escenario:* Está interesado en comprar una motocicleta y quiere explorar las opciones disponibles en Rallye Motor's. Le interesa una moto de bajo cilindraje y desea saber cuánto le costaría aproximadamente.
>
> *Instrucción:* Explore el catálogo, encuentre una motocicleta que le interese usando los filtros disponibles y genere una cotización.

**Preguntas posteriores:**
- ¿La información de cada motocicleta fue suficiente para orientar una decisión?
- ¿Le resultó fácil encontrar el botón para cotizar?
- ¿El resumen de la cotización fue claro y comprensible?

**Criterios de evaluación:**
- Al menos 2 de 3 usuarios encuentran una moto de su interés usando los filtros sin ayuda del moderador.
- Al menos 2 de 3 usuarios generan una cotización en menos de 3 minutos.
- Al menos 2 de 3 usuarios consideran que la información del catálogo es suficiente para orientar una decisión de compra.

---

### Hipótesis 5 — Panel administrativo: agenda y diagnóstico

**Hipótesis:** Si el panel administrativo presenta la agenda del día de forma clara y permite registrar diagnósticos sobre citas ya atendidas desde una interfaz simple, el usuario con rol de administrador podrá gestionar la operación diaria del taller sin necesidad de capacitación adicional.

> Esta hipótesis aplica únicamente al **Usuario 1 (Product Owner)**, quien simulará el rol de administrador del local durante la prueba.

**Condiciones previas:**
- **Credenciales:** las credenciales del administrador (usuario y contraseña) son registradas en la base de datos por el equipo técnico antes de la prueba y entregadas al Usuario 1 al inicio de esta tarea. El participante solo debe ingresarlas para acceder; el registro de credenciales no hace parte de la evaluación.
- **Cita atendida:** el moderador habrá pre-cargado en el sistema una cita con estado *atendida* antes de iniciar esta tarea, dado que el sistema solo permite registrar diagnósticos sobre citas cuya atención ya fue completada.

**Preguntas previas:**
- ¿Cómo organizaría la agenda de servicios técnicos de un local si tuviera que gestionarla?
- ¿Qué información necesitaría ver de cada cita para organizar bien el día de trabajo?

**Tarea 5**

> *Escenario:* Es el encargado de gestionar el panel del local. El equipo le ha proporcionado sus credenciales de acceso. Tiene citas agendadas para hoy y necesita revisar la agenda del día. Además, hay una cita que ya fue atendida y debe registrar el diagnóstico del servicio realizado para generar el radicado del cliente.
>
> *Instrucción:* Ingrese al panel administrativo con las credenciales proporcionadas, revise las citas del día y registre el diagnóstico sobre la cita que ya fue atendida.

**Preguntas posteriores:**
- ¿La agenda le permitió ver claramente las citas del día y su estado?
- ¿El formulario de diagnóstico fue fácil de completar?
- ¿Quedó claro que el radicado fue generado correctamente tras registrar el diagnóstico?
- ¿Hubo algún elemento del panel que le pareciera confuso, innecesario o que esperaba encontrar de otra forma?

**Criterios de evaluación:**
- El usuario accede al panel administrativo con las credenciales proporcionadas sin dificultad.
- El usuario identifica las citas del día y distingue correctamente cuál tiene estado *atendida* y sobre cuál puede registrar el diagnóstico.
- El usuario registra el diagnóstico y verifica la generación del radicado en menos de 3 minutos.

---

## 2.11 Formulario de observación por sesión

> Uno por sesión. Completado en tiempo real por el observador de métricas y el observador de comportamiento.

**Datos generales**

| Campo | Valor |
|---|---|
| Número de sesión | |
| Fecha | |
| Usuario | |
| Moderador principal | |
| Observador de métricas | |
| Observador de comportamiento | |

---

**Registro por tarea**

Repetir el siguiente bloque para cada hipótesis evaluada (H1 a H5 según corresponda al usuario).

---

**Hipótesis ___**

| Campo | Registro |
|---|---|
| Hora de inicio | |
| Hora de finalización | |
| Tiempo total (segundos) | |
| Tarea completada sin ayuda | Sí / No |
| Número de errores de navegación | |

**Detalle de errores de navegación**

| # | Tipo de error | Elemento de la interfaz | Momento aproximado |
|---|---|---|---|
| 1 | | | |
| 2 | | | |
| 3 | | | |

**Intervenciones del moderador**

| # | Momento | Motivo del bloqueo | Pregunta utilizada |
|---|---|---|---|
| 1 | | | |

**Comentarios espontáneos del usuario** *(pensamiento en voz alta)*

```
[Transcribir literalmente los comentarios más relevantes]
```

**Expresiones no verbales observadas**

| Momento | Expresión / reacción | Elemento asociado |
|---|---|---|
| | | |

---

## 2.12 Formulario de post-prueba por participante

> Aplicado por el moderador al finalizar todas las tareas, antes del cuestionario SUS.

**Preguntas de percepción general**

1. En general, ¿cómo describiría su experiencia usando la plataforma?
2. ¿Qué parte del sistema le resultó más fácil de usar? ¿Por qué?
3. ¿En qué momento tuvo más dificultades? ¿Qué cree que lo causó?
4. ¿Hay algo que esperaba encontrar en la plataforma y no encontró?
5. ¿Qué cambiaría o mejoraría si pudiera modificar algo del sistema?

**Respuestas a preguntas posteriores por tarea** *(resumen del moderador)*

| Tarea | Pregunta | Respuesta resumida |
|---|---|---|
| H1 | ¿Encontró fácilmente la opción para agendar? | |
| H1 | ¿Hubo algún paso confuso? | |
| H1 | ¿La información solicitada fue comprensible? | |
| H2 | ¿Fue fácil encontrar la cita por placa? | |
| H2 | ¿El proceso de cancelación fue claro? | |
| H2 | ¿Quedó claro que la cita fue cancelada? | |
| H3 | ¿Las preguntas del sistema fueron fáciles de responder? | |
| H3 | ¿Los resultados correspondían a lo buscado? | |
| H3 | ¿Hubo confusión en algún punto del flujo? | |
| H4 | ¿La información del catálogo fue suficiente? | |
| H4 | ¿Fue fácil encontrar el botón de cotizar? | |
| H4 | ¿El resumen de cotización fue claro? | |
| H5 *(solo U1)* | ¿La agenda mostró claramente las citas y su estado? | |
| H5 *(solo U1)* | ¿El formulario de diagnóstico fue fácil de completar? | |
| H5 *(solo U1)* | ¿Quedó claro que el radicado fue generado? | |
| H5 *(solo U1)* | ¿Hubo algún elemento confuso en el panel? | |

---

## 2.13 Cuestionario SUS — System Usability Scale

Al finalizar todas las tareas, cada participante calificará las siguientes afirmaciones en una escala de **1 (Totalmente en desacuerdo)** a **5 (Totalmente de acuerdo)**:

| # | Afirmación |
|---|---|
| 1 | Me gustaría usar esta plataforma con frecuencia. |
| 2 | La plataforma me pareció más compleja de lo necesario. *(Inversa)* |
| 3 | Consideré que la plataforma era fácil de usar. |
| 4 | Necesité ayuda de alguien para poder usar la plataforma. *(Inversa)* |
| 5 | Las funciones de la plataforma estaban bien integradas entre sí. |
| 6 | Me pareció que la plataforma tenía demasiadas inconsistencias. *(Inversa)* |
| 7 | Aprendí a usar la plataforma con rapidez. |
| 8 | Encontré la plataforma difícil de usar. *(Inversa)* |
| 9 | Me sentí seguro navegando en la plataforma. |
| 10 | Necesité aprender muchas cosas nuevas antes de poder usarla. *(Inversa)* |

> Un puntaje SUS ≥ 68 puntos indica una experiencia de usuario positiva y aceptable. Este resultado se calculará y analizará durante la ejecución de las pruebas en el Sprint 3.

---

## 2.14 Consentimiento informado

El consentimiento informado debe presentarse al participante al inicio de la sesión, antes de cualquier actividad. Su firma o aprobación es condición obligatoria para proceder.

El documento debe incluir:

- Nombre del proyecto y equipo responsable
- Propósito de la sesión (evaluación del sistema, no del usuario)
- Descripción de las actividades que realizará el participante
- Autorización para la grabación en video de la sesión
- Confirmación de que los datos serán usados exclusivamente con fines académicos
- Garantía de anonimato en el análisis y reporte de resultados
- Derecho del participante a retirarse en cualquier momento sin consecuencias
- Espacio para nombre, firma y fecha

> El equipo debe conservar una copia firmada por cada participante.
