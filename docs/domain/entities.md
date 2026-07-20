# Entidades del dominio de AutoCam

Este documento describe identidad, responsabilidad y comportamiento del
dominio. No es un modelo de tablas ni una especificación de persistencia.

## Usuario

**Descripción:** Persona global que puede pertenecer a uno o más tenants.

**Responsabilidad:** Representar identidad y autoría de acciones relevantes.

**Propietario del dato:** AutoCam administra la identidad; la organización
administra su pertenencia y roles.

**Ciclo de vida:** Creado, incorporado a organizaciones, puede acumular roles y
pertenencias, y puede dejar de pertenecer a una organización.

**Invariantes:** Email único; los roles válidos son cerrados; una combinación
de roles no elimina las restricciones específicas de cada decisión.

**Reglas:** La persona puede ser Dueño de Flota y Chofer simultáneamente.

**Relacionadas:** Membresía, Taller, Flota, Checklist, Orden de Trabajo.

**Eventos:** Usuario incorporado, Rol asignado, Membresía retirada.

## Membresía

**Descripción:** Pertenencia de un Usuario a un Taller o Flota con uno o más
roles.

**Responsabilidad:** Delimitar autoridad de una persona dentro de una
organización.

**Propietario del dato:** La organización que concede la pertenencia.

**Ciclo de vida:** Invitada, aceptada, activa, retirada.

**Invariantes:** Una persona no debe tener identidades duplicadas para combinar
roles dentro del mismo tenant.

**Reglas:** La autorización se interpreta en el contexto de la organización,
no solo por el rol global.

**Relacionadas:** Usuario, Taller, Flota.

**Eventos:** Invitación emitida, Invitación aceptada, Membresía activada,
Membresía retirada.

## Taller

**Descripción:** Organización que comercializa AutoCam y/o ejecuta trabajos.

**Responsabilidad:** Operar la relación con Flotas y prestar mantenimiento.

**Propietario del dato:** El propio Taller dentro de AutoCam.

**Ciclo de vida:** Registrado, operativo, eventualmente inactivo.

**Invariantes:** El Taller no se convierte en dueño de los vehículos por
ejecutar trabajos.

**Reglas:** V1 opera con un Taller real, aunque el dominio soporta múltiples
Talleres.

**Relacionadas:** Flota, Membresía, Orden de Trabajo, Plan de Mantenimiento.

**Eventos:** Taller registrado, Flota incorporada, Trabajo ejecutado.

## Flota

**Descripción:** Organización dueña de vehículos y de su historia operativa.

**Responsabilidad:** Gestionar activos y tomar decisiones comerciales de
mantenimiento.

**Propietario del dato:** La Flota.

**Ciclo de vida:** Incorporada, activa, en mora, bloqueada, suspendida,
purgada.

**Invariantes:** El historial del vehículo no depende de un Taller ejecutor.
La purga no ocurre sin mora, bloqueo, suspensión y retención completos.

**Reglas:** Puede conservar el historial y operar con AutoCam aunque cambie su
Taller comercial si deja de operar con el Taller anterior pero sigue pagando
AutoCam directamente. Reparar en otro taller no cambia `tallerComercialId`; el
historial de ejecución se adjunta sin fragmentar la Flota.

**Relacionadas:** Taller comercial, Vehículo, Usuario, Estado de Pago.

**Eventos:** Flota incorporada, Cambio de Taller comercial, Estado de pago
cambiado, Flota suspendida, Flota purgada.

## Vehículo

**Descripción:** Activo operativo identificado por patente y perteneciente a
una Flota.

**Responsabilidad:** Ser la unidad central de salud, kilometraje, mantenimiento,
vencimientos y alertas.

**Propietario del dato:** Flota.

**Ciclo de vida:** Incorporado, operativo, con restricciones por vencimiento,
retirado o purgado según el ciclo de la Flota.

**Invariantes:** Pertenece a una Flota; el kilometraje vigente no retrocede;
su historial no se fragmenta por cambio de Taller.

**Reglas:** Un salto sospechoso de kilometraje se revisa, no se descarta
automáticamente.

**Relacionadas:** Checklist, Cubierta, Vencimiento, Orden de Trabajo, Alerta,
Plan de Mantenimiento.

**Eventos:** Vehículo incorporado, Kilometraje actualizado, Vehículo bloqueado,
Vehículo retirado.

## Cubierta

**Descripción:** Unidad física identificable, con historial propio de uso.

**Responsabilidad:** Permitir seguimiento de posición, kilometraje, recapados y
vida útil.

**Propietario del dato:** Flota, aunque el dato pueda ser operado por el
Taller.

**Ciclo de vida:** Adquirida, activa, asignada, rotada/desmontada, inactiva.

**Invariantes:** Una asignación se cierra con su kilometraje final; el
kilometraje vigente se deriva de la asignación abierta y el vehículo.

**Reglas:** No se mantienen dos fuentes paralelas para el kilometraje de una
asignación vigente.

**Relacionadas:** Vehículo, Asignación de Cubierta.

**Eventos:** Cubierta adquirida, Cubierta asignada, Cubierta rotada, Cubierta
recapada, Cubierta retirada.

## Vencimiento

**Descripción:** Fecha límite de un documento o condición del vehículo, como
VTV o seguro.

**Responsabilidad:** Señalar una obligación temporal y si su vencimiento
restringe la operación.

**Propietario del dato:** Flota.

**Ciclo de vida:** Registrado, próximo a vencer, vencido, renovado o eliminado
por corrección autorizada.

**Invariantes:** El estado de bloqueo debe respetar la regla asociada al
vencimiento.

**Reglas:** Un vencimiento bloqueante impide asignaciones operativas cuando
corresponda.

**Relacionadas:** Vehículo, Alerta.

**Eventos:** Vencimiento registrado, Vencimiento próximo, Vencimiento vencido,
Documento renovado.

## Checklist

**Descripción:** Evidencia diaria e inmutable producida por el Chofer.

**Responsabilidad:** Registrar frenos, luces, aceite, anomalías, kilometraje y
condición temporal del reporte.

**Propietario del dato:** Flota como historial del vehículo; el Chofer es
autor del hecho.

**Ciclo de vida:** Creado offline, sincronizado a tiempo o tardío, corregido
mediante nuevo checklist, conservado como evidencia.

**Invariantes:** Nunca se actualiza ni se sobrescribe; una corrección referencia
el registro anterior.

**Reglas:** `timestampSyncAt` decide la ventana de 48 horas. Odómetro
inoperativo permite completar el checklist. Un reloj sospechoso fuerza
validación tardía.

**Relacionadas:** Usuario, Chofer, Vehículo, Alerta, Corrección.

**Eventos:** Checklist completado, Checklist sincronizado, Anomalía reportada,
Checklist tardío, Checklist corregido.

## Plan de Mantenimiento

**Descripción:** Conjunto general de reglas preventivas para un tipo de
vehículo dentro de un Taller.

**Responsabilidad:** Definir escalones y tareas esperadas.

**Propietario del dato:** Taller.

**Ciclo de vida:** Creado, vigente, reemplazado como referencia por cambios
posteriores, conservado para interpretación histórica.

**Invariantes:** V1 no permite personalización por Flota.

**Reglas:** Cambiar el plan no modifica alertas ya calculadas.

**Relacionadas:** Taller, Escalón de Mantenimiento, Vehículo.

**Eventos:** Plan creado, Plan actualizado, Regla futura modificada.

## Escalón de Mantenimiento

**Descripción:** Versión de una regla que se activa al alcanzar un kilometraje.

**Responsabilidad:** Determinar tareas preventivas y su vigencia histórica.

**Propietario del dato:** Taller.

**Ciclo de vida:** Vigente desde una fecha, cerrado por nueva versión,
histórico.

**Invariantes:** No se edita con UPDATE conceptual; toda modificación crea
versión nueva y cierra la anterior.

**Reglas:** La versión aplicable depende del momento del cálculo, no de la
versión actual consultada retroactivamente.

**Relacionadas:** Plan, Cumplimiento, Vehículo, Alerta.

**Eventos:** Escalón vigente, Escalón reemplazado.

## Cumplimiento de Escalón

**Descripción:** Registro de lo que realmente ocurrió con las tareas de un
escalón para un vehículo.

**Responsabilidad:** Conservar tareas realizadas, rechazadas y motivos.

**Propietario del dato:** Flota sobre su vehículo, con participación del
Taller.

**Ciclo de vida:** Abierto, parcialmente cumplido, cumplido con deuda o
cerrado.

**Invariantes:** El rechazo comercial requiere autoridad de Dueño de Flota.

**Reglas:** Las tareas rechazadas pueden arrastrarse; falta de stock y
postergación del Taller no equivalen a rechazo del cliente.

**Relacionadas:** Vehículo, Escalón, Orden de Trabajo, Usuario.

**Eventos:** Cumplimiento registrado, Tarea rechazada, Deuda creada, Deuda
resuelta, Deuda escalada.

## Diagnóstico

**Descripción:** Resultado de la inspección del Mecánico con tareas técnicas
sugeridas.

**Responsabilidad:** Servir de base técnica para crear una Orden de Trabajo.

**Propietario del dato:** Taller ejecutor.

**Ciclo de vida:** Registrado, vinculado a una orden, conservado como antecedente
del trabajo.

**Invariantes:** No implica aprobación comercial de la Flota.

**Reglas:** La orden nace del diagnóstico; las tareas descubiertas después se
modelan como reparaciones extras dentro de la misma orden.

**Relacionadas:** Orden de Trabajo, Vehículo, Mecánico, Tarea de Mantenimiento.

**Eventos:** Diagnóstico registrado, Orden creada desde diagnóstico.

## Tarea de Mantenimiento

**Descripción:** Unidad de trabajo técnico dentro de un Diagnóstico, Escalón u
Orden de Trabajo.

**Responsabilidad:** Representar una intervención concreta sobre el vehículo.

**Propietario del dato:** Taller para la propuesta; Flota para la decisión
comercial por tarea.

**Ciclo de vida:** Sugerida, pendiente de aprobación, aprobada, rechazada,
en ejecución, realizada, postergada operativamente o arrastrada como deuda.

**Invariantes:** El rechazo comercial requiere Dueño de Flota. Falta de stock y
postergación del Taller no equivalen a rechazo del cliente.

**Reglas:** La Flota aprueba o rechaza por tarea, no solo sobre la orden
completa.

**Relacionadas:** Diagnóstico, Orden de Trabajo, Cumplimiento de Escalón,
Reparación extra.

**Eventos:** Tarea sugerida, Tarea aprobada, Tarea rechazada, Tarea realizada,
Tarea arrastrada.

## Reparación extra

**Descripción:** Tarea agregada durante la ejecución de una orden, fuera del
Diagnóstico inicial.

**Responsabilidad:** Registrar trabajo adicional descubierto en el camino.

**Propietario del dato:** Taller ejecutor.

**Ciclo de vida:** Agregada, clasificada, notificada si no es menor, habilitada
por urgencia del Taller si corresponde, ejecutada o cancelada.

**Invariantes:** Debe quedar trazada dentro de la orden; no puede quedar fuera
del historial del vehículo.

**Reglas:** Mecánico y Admin del Taller pueden agregarla y clasificarla como
menor o no menor. Si es menor, no requiere notificación formal. Si no es menor,
se notifica al Admin del Taller y al Dueño de Flota. Si además es urgente, el
Admin del Taller la marca como habilitada por urgencia del Taller con modal
explícito, registrando `habilitadaPorId` y `modalConfirmadoAt`.

**Atributos de urgencia (parte de la reparación extra, no entidad separada):**

- `habilitadaPorUrgenciaTaller`
- `habilitadaPorId`
- `modalConfirmadoAt`
- `motivoUrgencia` (opcional)

**Relacionadas:** Orden de Trabajo, Diagnóstico, Tarea de Mantenimiento.

**Eventos:** Reparación extra agregada, Reparación extra clasificada,
Reparación extra notificada, Reparación extra habilitada por urgencia.

## Orden de Trabajo

**Descripción:** Unidad de trabajo técnico y decisión comercial sobre un
vehículo.

**Responsabilidad:** Coordinar diagnóstico, aprobación por tarea, ejecución,
reparaciones extras, vencimiento y cierre.

**Propietario del dato:** Taller ejecutor para la operación; Flota para las
decisiones comerciales por tarea y el historial del activo.

**Ciclo de vida:** Pendiente, parcialmente aprobada, en progreso, finalizada,
cancelada o reabierta como nueva orden vinculada.

**Invariantes:** Toda orden pendiente tiene SLA; vencer no aprueba; solo la
Flota rechaza por decisión comercial. Incluye observación de que pueden surgir
reparaciones extras.

**Reglas:** El Taller ejecutor puede ser diferente del Taller comercial. Admin
del Taller, Mecánico o Dueño de Flota pueden cancelar según el estado.

**Relacionadas:** Diagnóstico, Vehículo, Taller, Reparación extra, Cumplimiento,
Usuario.

**Eventos:** Orden creada, Tarea aprobada, Tarea rechazada, Reparación extra
agregada, Orden vencida, Trabajo iniciado, Trabajo finalizado, Orden cancelada,
Orden reabierta.

## Alerta

**Descripción:** Registro congelado de un riesgo o situación derivado por una
regla de salud.

**Responsabilidad:** Hacer visible el riesgo con tipo, severidad y origen.

**Propietario del dato:** AutoCam como resultado del motor; el contexto del
vehículo pertenece a la Flota.

**Ciclo de vida:** Generada, abierta, con corrección reportada, resuelta por
humano.

**Invariantes:** Una alerta crítica no se autorresuelve; cambiar un plan no
altera alertas previas.

**Reglas:** Las alertas se agrupan por vehículo antes de notificar en
plataforma y por email según severidad. `INFORMATIVA` y `URGENTE` pueden
resolverse por Admin del Taller, Mecánico o Dueño de Flota. `CRITICA` puede
cerrarse manualmente por esos roles con auditoría obligatoria.

**Relacionadas:** Vehículo, Checklist, Vencimiento, Cumplimiento.

**Eventos:** Alerta generada, Corrección reportada, Alerta resuelta.

## Estado de Pago

**Descripción:** Situación de acceso de una Flota respecto de su suscripción.

**Responsabilidad:** Expresar si la Flota está al día, en mora, bloqueada o
suspendida.

**Propietario del dato:** Taller comercial, mediante operación manual en V1.

**Ciclo de vida:** `AL_DIA` → `EN_MORA` → `BLOQUEADA` → `SUSPENDIDA` →
purga tras 180 días.

**Invariantes:** Las transiciones de V1 son manuales; la purga respeta el
período de retención y oportunidad de exportación.

**Reglas:** Bloqueada permite solo lectura/exportación; suspendida no permite
acceso operativo. Si la Flota regulariza desde `SUSPENDIDA`, el Admin del
Taller la pasa manualmente a `AL_DIA`, restaura acceso completo y detiene el
conteo de purga.

**Relacionadas:** Flota, Taller comercial, Historial de Estado.

**Eventos:** Mora iniciada, Acceso bloqueado, Suspensión iniciada,
Regularización, Purga habilitada.

## Invitación

**Descripción:** Propuesta para incorporar un usuario a una Flota o Taller.

**Responsabilidad:** Iniciar la pertenencia de un Usuario con un rol previsto.

**Propietario del dato:** Organización que invita.

**Ciclo de vida:** Emitida, aceptada, rechazada o revocada. En V1 no expira.

**Invariantes:** Aceptar una invitación no puede conceder un rol fuera del
conjunto cerrado.

**Reglas:** Admin del Taller invita cualquier rol. Dueño de Flota solo invita
choferes. Las invitaciones no expiran en V1. Cualquier Admin del Taller o
Dueño de Flota del tenant puede revocar una invitación pendiente.

**Relacionadas:** Usuario, Membresía, Flota, Taller.

**Eventos:** Invitación emitida, Invitación aceptada, Invitación rechazada,
Invitación revocada.
