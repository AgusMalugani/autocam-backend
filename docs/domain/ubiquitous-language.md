# Lenguaje ubicuo de AutoCam

Este vocabulario es normativo para el dominio. Los términos deben conservar su
significado en conversaciones de negocio, especificaciones y diseño.

## AutoCam

**Definición:** Plataforma SaaS B2B2B de marca blanca para gestión de flotas y
mantenimiento preventivo/predictivo.

**Representa:** Infraestructura digital que conecta Taller, Flota, Mecánico y
Chofer con trazabilidad compartida.

**No representa:** Una aplicación de telemetría, una pasarela de pagos ni un
taller mecánico.

**Sinónimos prohibidos:** “App del taller”, “sistema de GPS”, “sistema de cobro”.

**Fuentes:** Constitución, Resumen Ejecutivo, Resumen Comercial.

## Taller

**Definición:** Organización que comercializa el servicio a las Flotas y
ejecuta trabajos de mantenimiento, directamente o mediante un taller externo.

**Representa:** Actor comercial y operativo del mantenimiento.

**No representa:** El propietario de los vehículos ni necesariamente el
ejecutor de cada trabajo.

**Sinónimos prohibidos:** “Dueño del camión”, “Flota”, “tenant ejecutor” como
sinónimo de propietario.

**Fuentes:** Constitución, schema, resúmenes.

## Flota

**Definición:** Empresa de transporte propietaria de los vehículos y del
historial de mantenimiento.

**Representa:** Dueño del activo y autoridad de las decisiones comerciales de
mantenimiento.

**No representa:** Una colección de vehículos sin propietario, ni el Taller que
factura la suscripción.

**Reglas importantes:** Puede mantener una relación comercial con un Taller y
ejecutar una orden en otro sin cambiar `tallerComercialId`. Reparar en otro
taller adjunta historial de ejecución; la Flota sigue pagando membresía al
Taller comercial. `tallerComercialId` solo cambia si la Flota deja de operar
con ese Taller pero continúa pagando AutoCam directamente.

**Sinónimos prohibidos:** “Taller cliente”, “vehículo”, “cuenta de pago”.

**Fuentes:** Constitución, schema, resúmenes.

## Dueño de Flota

**Definición:** Rol que representa a la persona autorizada a tomar decisiones
comerciales por la Flota.

**Representa:** Única autoridad para rechazar tareas y aprobar o rechazar
tareas dentro de una orden por parte del cliente.

**No representa:** Al Mecánico ni al administrador del Taller.

**Sinónimos prohibidos:** “Aprobador genérico”, “cliente” cuando se pierde la
distinción de autoridad.

**Fuentes:** Constitución, schema, resúmenes.

## Vehículo

**Definición:** Activo móvil perteneciente a una Flota, identificado por su
patente y asociado a un tipo de vehículo.

**Representa:** Unidad cuya salud, kilometraje, mantenimiento, vencimientos y
alertas se gestionan.

**No representa:** Un activo del Taller ni una orden de trabajo.

**Reglas importantes:** Su identidad e historial permanecen con la Flota aunque
el trabajo sea ejecutado por otro Taller.

**Sinónimos prohibidos:** “Camión” como término universal si también se
gestionan otros tipos de vehículo; “unidad del taller”.

**Fuentes:** Constitución, schema, resúmenes.

## Chofer

**Definición:** Rol de campo que opera el vehículo y registra diariamente su
estado y kilometraje.

**Representa:** Fuente diaria de observaciones operativas del vehículo.

**No representa:** Autoridad para aprobar gastos ni fuente infalible de tiempo o
kilometraje.

**Reglas importantes:** Puede reportar un odómetro inoperativo; un error de
campo se marca para revisión, no se descarta automáticamente.

**Sinónimos prohibidos:** “Inspector”, “aprobador”, “sensor humano”.

**Fuentes:** Constitución, schema, resúmenes.

## Mecánico

**Definición:** Rol técnico que diagnostica vehículos y registra tareas de
mantenimiento ejecutadas o impedidas operativamente.

**Representa:** Autoridad técnica de ejecución.

**No representa:** Autoridad comercial de la Flota.

**Reglas importantes:** Puede indicar falta de stock o postergación solicitada
por el Taller, pero no rechazar una tarea por el cliente.

**Sinónimos prohibidos:** “Aprobador”, “Dueño de Flota”.

**Fuentes:** Constitución, schema, resúmenes.

## Checklist

**Definición:** Registro diario e inmutable del Chofer sobre estado, anomalías,
kilometraje y momento de observación del vehículo.

**Representa:** Evidencia operativa diaria y origen posible de una alerta.

**No representa:** Un formulario editable ni una garantía de que el dato sea
correcto.

**Reglas importantes:** Una corrección crea otro checklist que referencia al
anterior. `timestampLocal` es narrativo; `timestampSyncAt` es el ancla de
confianza para la sincronización.

**Sinónimos prohibidos:** “Reporte editable”, “inspección definitiva”.

**Fuentes:** Constitución, schema.

## Checklist tardío

**Definición:** Checklist sincronizado fuera de la ventana offline de 48 horas
o con reloj sospechoso.

**Representa:** Evidencia conservada que requiere validación humana.

**No representa:** Un checklist inválido o descartado.

**Sinónimos prohibidos:** “Checklist perdido”, “checklist rechazado”.

**Fuentes:** Constitución, schema, Resumen Ejecutivo.

## Kilometraje sospechoso

**Definición:** Kilometraje aceptado como reporte, pero con un salto superior al
umbral diario razonable.

**Representa:** Dato pendiente de revisión humana.

**No representa:** Un checklist rechazado ni una corrección automática.

**Sinónimos prohibidos:** “Kilometraje inválido” como estado definitivo.

**Fuentes:** Constitución, schema.

## Plan de Mantenimiento

**Definición:** Plantilla general del Taller que define escalones y tareas según
el tipo de vehículo.

**Representa:** Regla preventiva aplicable a un conjunto de vehículos.

**No representa:** Un plan personalizado por Flota en V1 ni una alerta.

**Reglas importantes:** Sus cambios solo afectan cálculos futuros.

**Sinónimos prohibidos:** “Plan de una Flota”, “presupuesto”.

**Fuentes:** Constitución, schema.

## Escalón de Mantenimiento

**Definición:** Versión vigente o histórica de un umbral de kilometraje y sus
tareas.

**Representa:** Regla de mantenimiento con vigencia temporal auditable.

**No representa:** Un nivel que pueda editarse sin conservar historial.

**Reglas importantes:** Se versiona append-only; una versión anterior se cierra
y una nueva toma vigencia.

**Sinónimos prohibidos:** “Service” cuando se oculta el umbral y la vigencia.

**Fuentes:** Constitución, schema.

## Deuda de mantenimiento / Arrastre

**Definición:** Tarea rechazada o no resuelta que reaparece en un escalón
posterior.

**Representa:** Riesgo técnico pendiente y decisión histórica de la Flota.

**No representa:** Una tarea nueva sin antecedente ni un simple recordatorio.

**Reglas importantes:** Su urgencia puede subir de informativa a urgente al
superar el umbral de escalada.

**Sinónimos prohibidos:** “Tarea cancelada” si sigue pendiente.

**Fuentes:** Constitución, schema, resúmenes.

## Orden de Trabajo

**Definición:** Compromiso operativo que describe mantenimiento a ejecutar sobre
un vehículo por un Taller.

**Representa:** Unidad de ejecución y aprobación comercial.

**No representa:** La suscripción de la Flota ni el historial completo del
vehículo.

**Reglas importantes:** Tiene estado explícito, SLA y puede ser ejecutada por
un Taller distinto del Taller comercial. Nace de un Diagnóstico base y puede
incorporar reparaciones extras durante la ejecución. La Flota decide por tarea,
no solo sobre la orden completa. Incluye una observación de que pueden surgir
reparaciones adicionales durante el trabajo.

**Sinónimos prohibidos:** “Presupuesto aprobado”, “ticket” cuando se pierde la
responsabilidad comercial.

**Fuentes:** Constitución, schema, resúmenes, decisiones de dominio V1.

## Diagnóstico

**Definición:** Resultado de la inspección del Mecánico que incluye tareas
técnicas sugeridas antes de crear una Orden de Trabajo.

**Representa:** Base técnica inicial del trabajo a realizar.

**No representa:** Una orden aprobada ni una decisión comercial de la Flota.

**Reglas importantes:** La orden se construye a partir del diagnóstico; durante
`EN_PROGRESO` pueden agregarse tareas nuevas descubiertas en el camino.

**Sinónimos prohibidos:** “Presupuesto”, “Orden de trabajo” antes de su
formalización.

**Fuentes:** Decisiones de dominio V1.

## Reparación extra

**Definición:** Tarea agregada a una Orden de Trabajo en ejecución que no estaba
en el Diagnóstico inicial.

**Representa:** Trabajo adicional descubierto durante la intervención.

**No representa:** Una tarea rechazada ni una deuda de escalón.

**Reglas importantes:** Mecánico y Admin del Taller pueden agregarla y
clasificarla como menor o no menor al registrarla. Si es menor, no requiere
notificación formal. Si no es menor, se notifica al Admin del Taller y al
Dueño de Flota. Si además es urgente, el Admin del Taller la marca como
**habilitada por urgencia del Taller** con confirmación explícita (modal),
registrando quién habilitó y cuándo. No reemplaza la aprobación comercial del
Dueño de Flota.

**Sinónimos prohibidos:** “Sorpresa no registrada”, “ajuste informal”,
“aprobada por el taller” cuando se confunde con aprobación comercial de la
Flota.

**Fuentes:** Decisiones de dominio V1.

## Reparación extra menor

**Definición:** Reparación extra clasificada por el Taller como de bajo
impacto operativo o comercial al momento de registrarla.

**Representa:** Trabajo adicional que puede ejecutarse sin notificación formal
a la Flota.

**No representa:** Una tarea aprobada comercialmente por el Dueño de Flota.

**Reglas importantes:** La clasificación la realiza Mecánico o Admin del
Taller al agregar la tarea.

**Sinónimos prohibidos:** “Sin costo”, “sin registro”.

**Fuentes:** Decisiones de dominio V1.

## Habilitada por urgencia del Taller

**Definición:** Estado de una reparación extra no menor urgente que el Admin
del Taller habilita para ejecución inmediata con confirmación explícita.

**Representa:** Habilitación operativa del Taller, no decisión comercial de la
Flota.

**No representa:** Aprobación comercial del Dueño de Flota ni autoaprobación
silenciosa.

**Reglas importantes:** Se registra al cargar la tarea: `habilitadaPorUrgenciaTaller`,
`habilitadaPorId`, `modalConfirmadoAt` y, opcionalmente, `motivoUrgencia`.

**Sinónimos prohibidos:** “Aprobada por el taller”, “aprobación automática”,
“urgencia implícita”.

**Fuentes:** Decisiones de dominio V1.

## Invitación

**Definición:** Propuesta para incorporar un usuario a un Taller o Flota con
un rol determinado.

**Representa:** Inicio formal de una Membresía futura.

**No representa:** Una Membresía activa ni un acceso inmediato.

**Reglas importantes:** Admin del Taller invita cualquier rol. Dueño de Flota
solo invita choferes. No expira en V1. Cualquier Admin del Taller o Dueño de
Flota del tenant puede revocar una invitación pendiente.

**Sinónimos prohibidos:** “Alta directa” cuando aún no fue aceptada.

**Fuentes:** Decisiones de dominio V1.

## Orden cancelada

**Definición:** Estado terminal de una Orden de Trabajo interrumpida antes de
su cierre normal.

**Representa:** Fin anticipado del compromiso operativo.

**No representa:** Un rechazo comercial de tareas ni una finalización exitosa.

**Reglas importantes:** Admin del Taller, Mecánico o Dueño de Flota pueden
cancelar según el estado vigente. Puede reabrirse como nueva orden vinculada a
la cancelada.

**Sinónimos prohibidos:** “Rechazada” cuando la decisión fue comercial por
tarea.

**Fuentes:** schema, decisiones de dominio V1.

## Notificación

**Definición:** Entrega de una alerta o evento relevante a los actores
responsables por un canal definido.

**Representa:** Comunicación derivada de un hecho de dominio ya persistido.

**No representa:** La alerta en sí ni un cobro.

**Reglas importantes:** En V1 incluye alertas en plataforma y email a Dueño de
Flota y Admin del Taller según severidad.

**Sinónimos prohibidos:** Usar “notificación” como sinónimo de Alerta.

**Fuentes:** Decisiones de dominio V1.

## SLA de respuesta

**Definición:** Plazo máximo para que la Flota responda una Orden de Trabajo
pendiente.

**Representa:** Límite operativo que evita el bloqueo indefinido.

**No representa:** Una autorización automática del gasto.

**Reglas importantes:** Al vencer, la orden escala al `ADMIN_TALLER` y requiere
intervención humana.

**Sinónimos prohibidos:** “Autoaprobación”, “aprobación por silencio”.

**Fuentes:** Constitución, schema.

## Alerta

**Definición:** Resultado persistido de una regla de salud, mantenimiento,
inactividad, vencimiento o anomalía.

**Representa:** Riesgo o situación que requiere atención sobre un vehículo.

**No representa:** Un mensaje efímero ni una decisión automática de cierre.

**Reglas importantes:** Se congela al generarse, se deduplica por vehículo y
una alerta crítica nunca se autorresuelve. Las alertas `INFORMATIVA` y
`URGENTE` pueden resolverse por Admin del Taller, Mecánico o Dueño de Flota.
Las alertas `CRITICA` pueden cerrarse manualmente por esos mismos roles, con
auditoría obligatoria.

**Sinónimos prohibidos:** “Notificación” cuando se confunde el diagnóstico con
su canal de entrega.

**Fuentes:** Constitución, schema, resúmenes.

## Taller comercial

**Definición:** Taller que mantiene la relación de facturación de la Flota.

**Representa:** Quién cobra la suscripción.

**No representa:** Necesariamente quién ejecuta cada reparación.

**Sinónimos prohibidos:** “Taller del vehículo”.

**Fuentes:** Constitución, schema.

## Taller ejecutor

**Definición:** Taller que realiza una Orden de Trabajo concreta.

**Representa:** Quién ejecutó el trabajo puntual.

**No representa:** El propietario del vehículo ni necesariamente el Taller
comercial.

**Sinónimos prohibidos:** “Taller de la Flota” si se borra la distinción
comercial/operativa.

**Fuentes:** Constitución, schema.

## Estado de pago

**Definición:** Estado operativo de la cuenta de una Flota respecto de su
suscripción.

**Representa:** Acceso permitido y etapa del ciclo de mora.

**No representa:** Un cobro automático ni una factura.

**Reglas importantes:** V1 lo actualiza manualmente el Admin del Taller y no
se purgan datos antes del ciclo completo.

**Fuentes:** Constitución, schema, Resumen Comercial.

## Inmutabilidad histórica

**Definición:** Principio por el cual la evidencia y reglas vigentes no se
sobrescriben.

**Representa:** Capacidad de reconstruir qué se sabía y qué regla regía en un
momento pasado.

**No representa:** Inmutabilidad de todos los datos operativos del sistema.

**Sinónimos prohibidos:** “No se puede corregir”. Las correcciones se agregan
como nuevos hechos relacionados.

**Fuentes:** Constitución, schema, Resumen Ejecutivo.

## Notas de dominio V1

- No existe un rol formal distinto de `DUENIO_FLOTA` para administrar la Flota;
  esa autoridad recae en el Dueño de Flota.
- “Mantenimiento preventivo” y “mantenimiento predictivo” describen una única
  estrategia progresiva de producto, no categorías separadas en V1.
