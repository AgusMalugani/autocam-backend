# Product Backlog

## Objetivo

Este documento contiene las capacidades funcionales oficiales de AutoCam. Es
el punto de entrada para seleccionar qué Feature se desarrollará a
continuación mediante SpecKit.

El backlog no contiene historias de usuario, tareas, especificaciones ni
decisiones técnicas. Cada Feature seleccionada debe recorrer posteriormente el
flujo:

```text
Constitution → Specification → Clarification → Planning → Tasks → Implementation
```

El orden refleja dependencias funcionales y desbloqueo de capacidades, no una
estimación de esfuerzo.

## Convenciones

### Estados

- Pendiente
- En progreso
- Completada
- Bloqueada

### Prioridades

- P0 - Crítica
- P1 - Alta
- P2 - Media
- P3 - Baja

### Identificadores

Los identificadores son semánticos, cortos y estables. No representan
numeración de ejecución. SpecKit asignará numeraciones cuando una Feature sea
seleccionada.

# Features

## IDENTITY_ACCESS

Nombre

Identidad y acceso

Estado

Pendiente

Prioridad

P0

Descripción

Gestionar la identidad global de las personas, su pertenencia a Talleres o
Flotas y los cuatro roles válidos del dominio.

Valor para el negocio

Permite que cada actor opere dentro de la organización correcta y mantiene
separadas las responsabilidades del Taller, la Flota, el Mecánico y el
Chofer.

Dependencias

Ninguna

Desbloquea

WORKSHOP_ONBOARDING, FLEET_MANAGEMENT, USER_INVITATIONS, DRIVER_MANAGEMENT y
todas las capacidades operativas.

MVP

Sí

Observaciones

Los roles son `ADMIN_TALLER`, `MECANICO`, `DUENIO_FLOTA` y `CHOFER`. Un usuario
puede tener más de un rol dentro del mismo tenant.

## WORKSHOP_ONBOARDING

Nombre

Alta y gestión del Taller

Estado

Pendiente

Prioridad

P0

Descripción

Permitir incorporar y administrar el Taller como organización comercial y
operativa de AutoCam.

Valor para el negocio

El Taller es el cliente directo, canal de adquisición de Flotas y responsable
de operar el servicio.

Dependencias

IDENTITY_ACCESS

Desbloquea

FLEET_MANAGEMENT, MAINTENANCE_PLANS, WORKSHOP_BRANDING y la operación del
Taller.

MVP

Sí

Observaciones

V1 comienza con un Taller real, pero la capacidad debe respetar el aislamiento
entre organizaciones previsto por el producto.

## FLEET_MANAGEMENT

Nombre

Alta y gestión de Flotas

Estado

Pendiente

Prioridad

P0

Descripción

Permitir incorporar empresas de transporte, administrar su pertenencia al
Taller comercial y conservar la propiedad de sus activos e historial.

Valor para el negocio

Constituye la relación principal Taller–Flota y permite que el Taller
fidelice clientes corporativos sin apropiarse de sus datos.

Dependencias

IDENTITY_ACCESS, WORKSHOP_ONBOARDING

Desbloquea

VEHICLE_MANAGEMENT, DRIVER_MANAGEMENT, PAYMENT_STATUS, HISTORY_EXPORT y la
gestión operativa de cada Flota.

MVP

Sí

Observaciones

Reparar un vehículo en otro Taller no cambia automáticamente el Taller
comercial de la Flota.

## USER_INVITATIONS

Nombre

Invitaciones y membresías

Estado

Pendiente

Prioridad

P0

Descripción

Permitir invitar usuarios a Talleres o Flotas, aceptar o rechazar
invitaciones, crear membresías y revocar invitaciones pendientes.

Valor para el negocio

Permite que el Taller incorpore Flotas y que cada Flota incorpore sus
Choferes sin crear identidades duplicadas.

Dependencias

IDENTITY_ACCESS, WORKSHOP_ONBOARDING, FLEET_MANAGEMENT

Desbloquea

DRIVER_MANAGEMENT, operación del Mecánico, operación del Dueño de Flota y
CHECKLISTS.

MVP

Sí

Observaciones

El Admin del Taller puede invitar cualquier rol. El Dueño de Flota solo puede
invitar Choferes. Las invitaciones no expiran en V1; cualquier Admin del
Taller o Dueño de Flota del tenant puede revocar una invitación pendiente.

## VEHICLE_MANAGEMENT

Nombre

Alta y gestión de Vehículos

Estado

Pendiente

Prioridad

P0

Descripción

Permitir registrar, identificar y administrar los vehículos pertenecientes a
una Flota, incluyendo tipo, patente y kilometraje vigente.

Valor para el negocio

El vehículo es la unidad central de salud, mantenimiento, historial,
vencimientos y alertas.

Dependencias

FLEET_MANAGEMENT

Desbloquea

DRIVER_MANAGEMENT, CHECKLISTS, DIAGNOSTICS, MAINTENANCE_PLANS,
EXPIRATIONS y TIRE_PASSPORT.

MVP

Sí

Observaciones

La identidad e historial del vehículo pertenecen a la Flota, no al Taller
ejecutor.

## DRIVER_MANAGEMENT

Nombre

Gestión de Choferes

Estado

Pendiente

Prioridad

P1

Descripción

Permitir asociar Choferes a una Flota y habilitarlos para operar vehículos y
registrar evidencia diaria.

Valor para el negocio

Integra al actor de campo, que es la fuente diaria de kilometraje y anomalías.

Dependencias

FLEET_MANAGEMENT, USER_INVITATIONS, VEHICLE_MANAGEMENT

Desbloquea

CHECKLISTS y el flujo de información de campo.

MVP

Sí

Observaciones

Una persona puede ser Dueño de Flota y Chofer simultáneamente.

## CHECKLISTS

Nombre

Checklist diario de campo

Estado

Pendiente

Prioridad

P0

Descripción

Permitir que el Chofer registre diariamente el estado del vehículo,
kilometraje y anomalías, incluso sin conectividad, conservando correcciones e
historial.

Valor para el negocio

Reemplaza reportes informales, evita perder información en ruta y alimenta
diariamente la prevención de fallas.

Dependencias

DRIVER_MANAGEMENT, VEHICLE_MANAGEMENT

Desbloquea

DIAGNOSTICS, PREDICTIVE_ENGINE, ALERTS y el historial operativo.

MVP

Sí

Observaciones

La ventana offline es de 48 horas. Los reportes tardíos se conservan, pero
requieren validación humana y no disparan automáticamente la regla de
anomalía. Los checklists son inmutables.

## DIAGNOSTICS

Nombre

Diagnóstico técnico

Estado

Pendiente

Prioridad

P0

Descripción

Permitir que el Mecánico registre el resultado de la inspección de un vehículo
y las tareas técnicas sugeridas como base de una Orden de Trabajo.

Valor para el negocio

Conserva el contexto del Chofer y reduce diagnósticos repetidos antes de
intervenir el vehículo.

Dependencias

VEHICLE_MANAGEMENT, CHECKLISTS, USER_INVITATIONS

Desbloquea

WORK_ORDERS y el historial técnico del vehículo.

MVP

Sí

Observaciones

El Diagnóstico no implica aprobación comercial. Durante la ejecución pueden
surgir Reparaciones extras.

## MAINTENANCE_PLANS

Nombre

Planes de mantenimiento escalonados

Estado

Pendiente

Prioridad

P0

Descripción

Permitir definir reglas generales de mantenimiento por tipo de vehículo,
organizadas en escalones de kilometraje y tareas.

Valor para el negocio

Transforma el mantenimiento reactivo en una regla preventiva auditable y
permite anticipar necesidades de servicio.

Dependencias

WORKSHOP_ONBOARDING, VEHICLE_MANAGEMENT

Desbloquea

MAINTENANCE_COMPLIANCE, PREDICTIVE_ENGINE y WORK_ORDERS.

MVP

Sí

Observaciones

V1 utiliza planes generales por tipo de vehículo. No incluye personalización
por Flota. Las versiones históricas son inmutables y los cambios no son
retroactivos.

## WORK_ORDERS

Nombre

Órdenes de Trabajo

Estado

Pendiente

Prioridad

P0

Descripción

Permitir crear, aprobar por tarea, ejecutar, cancelar y finalizar trabajos de
mantenimiento sobre vehículos, con SLA y separación entre Taller comercial y
Taller ejecutor.

Valor para el negocio

Formaliza el paso desde el diagnóstico hasta la reparación, asigna
responsabilidades y evita que una orden quede bloqueada indefinidamente.

Dependencias

DIAGNOSTICS, VEHICLE_MANAGEMENT, IDENTITY_ACCESS, FLEET_MANAGEMENT

Desbloquea

MAINTENANCE_COMPLIANCE, HISTORY_EXPORT y la trazabilidad completa del trabajo.

MVP

Sí

Observaciones

La Flota aprueba o rechaza por tarea. Las Reparaciones extras pueden agregarse
durante la ejecución. Una extra no menor urgente puede quedar habilitada por
urgencia del Taller mediante confirmación explícita del Admin, sin confundirse
con aprobación comercial de la Flota.

## MAINTENANCE_COMPLIANCE

Nombre

Cumplimiento y deuda de mantenimiento

Estado

Pendiente

Prioridad

P0

Descripción

Permitir registrar tareas realizadas, rechazadas o impedidas, trasladar tareas
no resueltas a escalones posteriores y aumentar su urgencia cuando corresponda.

Valor para el negocio

Hace visible la deuda de mantenimiento y conserva la responsabilidad histórica
de las decisiones de la Flota y del Taller.

Dependencias

MAINTENANCE_PLANS, WORK_ORDERS

Desbloquea

PREDICTIVE_ENGINE, ALERTS y la gestión histórica de mantenimiento.

MVP

Sí

Observaciones

Solo el Dueño de Flota puede rechazar una tarea por decisión comercial. Falta
de stock y postergación del Taller son estados operativos diferentes.

## TIRE_PASSPORT

Nombre

Pasaporte de Cubiertas

Estado

Pendiente

Prioridad

P1

Descripción

Permitir seguir cada cubierta física, su posición, kilometraje, rotaciones,
recapados y vida útil a lo largo de distintos vehículos.

Valor para el negocio

Reduce pérdidas y fallas asociadas a cubiertas, y aporta trazabilidad de un
activo de alto costo.

Dependencias

VEHICLE_MANAGEMENT

Desbloquea

Indicadores de salud de cubiertas y una gestión operativa más completa del
vehículo.

MVP

Sí

Observaciones

El kilometraje de una asignación vigente se interpreta a partir del vehículo y
la asignación; no se debe crear una segunda fuente histórica contradictoria.

## EXPIRATIONS

Nombre

Vencimientos y restricciones operativas

Estado

Pendiente

Prioridad

P1

Descripción

Permitir registrar VTV, seguros y otros vencimientos asociados a vehículos,
generar su seguimiento y bloquear asignaciones cuando corresponda.

Valor para el negocio

Evita que vencimientos críticos se descubran tarde y reduce riesgos legales y
operativos.

Dependencias

VEHICLE_MANAGEMENT

Desbloquea

ALERTS y la validación de disponibilidad operativa de vehículos.

MVP

Sí

Observaciones

El bloqueo depende de la regla definida para cada vencimiento.

## PREDICTIVE_ENGINE

Nombre

Motor predictivo de mantenimiento

Estado

Pendiente

Prioridad

P0

Descripción

Evaluar kilometraje, escalones, deuda, anomalías, inactividad y vencimientos
para identificar necesidades y riesgos de mantenimiento.

Valor para el negocio

Permite pasar de atender roturas a anticipar servicios y reducir downtime no
planificado sin depender de telemetría.

Dependencias

CHECKLISTS, MAINTENANCE_PLANS, MAINTENANCE_COMPLIANCE, EXPIRATIONS y
VEHICLE_MANAGEMENT

Desbloquea

ALERTS, NOTIFICATIONS y la previsión operativa del Taller.

MVP

Sí

Observaciones

Las alertas ya generadas quedan congeladas. La regla de inactividad considera
un vehículo operativo sin reporte durante siete días.

## ALERTS

Nombre

Alertas de salud y mantenimiento

Estado

Pendiente

Prioridad

P0

Descripción

Permitir generar, clasificar, agrupar, revisar y resolver alertas de desgaste,
anomalías, inactividad, vencimientos y deuda arrastrada.

Valor para el negocio

Convierte señales operativas dispersas en riesgos priorizados y accionables
para el Taller y la Flota.

Dependencias

PREDICTIVE_ENGINE, VEHICLE_MANAGEMENT, CHECKLISTS

Desbloquea

NOTIFICATIONS y la supervisión proactiva de la Flota.

MVP

Sí

Observaciones

Las alertas se agrupan por vehículo. Las críticas nunca se autorresuelven;
pueden cerrarse manualmente por Admin del Taller, Mecánico o Dueño de Flota,
con auditoría obligatoria.

## NOTIFICATIONS

Nombre

Notificaciones de negocio

Estado

Pendiente

Prioridad

P1

Descripción

Entregar alertas y eventos relevantes dentro de la plataforma y por email a
los actores responsables según severidad.

Valor para el negocio

Reduce tiempos de reacción y evita que anomalías, vencimientos o decisiones
pendientes queden sin atención.

Dependencias

ALERTS, IDENTITY_ACCESS

Desbloquea

La operación proactiva del Taller y la Flota.

MVP

Sí

Observaciones

La notificación es el canal de entrega; no debe confundirse con la Alerta,
que es el hecho de negocio persistido.

## PAYMENT_STATUS

Nombre

Ciclo de estado de pago y acceso

Estado

Pendiente

Prioridad

P1

Descripción

Permitir al Taller gestionar manualmente el estado de pago de una Flota y
aplicar las reglas de acceso, mora, bloqueo, suspensión, regularización y
retención.

Valor para el negocio

Permite operar el modelo de suscripción de V1 sin pasarela de pagos y protege
los datos de la Flota durante la mora.

Dependencias

FLEET_MANAGEMENT, IDENTITY_ACCESS

Desbloquea

HISTORY_EXPORT y la gestión comercial recurrente del Taller.

MVP

Sí

Observaciones

La secuencia es `AL_DIA`, `EN_MORA`, `BLOQUEADA`, `SUSPENDIDA` y purga después
de seis meses sin regularización. Desde `SUSPENDIDA`, la regularización manual
devuelve a `AL_DIA`, restaura acceso y detiene la purga.

## HISTORY_EXPORT

Nombre

Exportación y continuidad del historial

Estado

Pendiente

Prioridad

P1

Descripción

Permitir que la Flota conserve y exporte su historial de vehículos,
checklists, trabajos, alertas y decisiones antes de perder acceso o durante
una transición comercial.

Valor para el negocio

Refuerza la propiedad de los datos, evita vendor lock-in y cumple la
protección de la Flota antes de una eventual purga.

Dependencias

FLEET_MANAGEMENT, CHECKLISTS, WORK_ORDERS, PAYMENT_STATUS

Desbloquea

La continuidad de la Flota ante cambios de Taller y la purga controlada.

MVP

Sí

Observaciones

El contenido y formato exacto de la exportación serán definidos en la
Specification de esta Feature.

## COMMERCIAL_RELATIONSHIPS

Nombre

Relaciones comerciales entre Taller y Flota

Estado

Pendiente

Prioridad

P1

Descripción

Permitir conservar, cambiar y registrar la relación comercial de una Flota
con su Taller, diferenciándola del Taller que ejecuta cada trabajo.

Valor para el negocio

Permite que la Flota repare en otros Talleres sin perder historial y que
continúe utilizando AutoCam aunque cambie su operación comercial.

Dependencias

FLEET_MANAGEMENT, PAYMENT_STATUS, HISTORY_EXPORT

Desbloquea

La red futura de Talleres y la continuidad comercial de AutoCam.

MVP

Sí

Observaciones

Reparar en otro Taller ejecutor no cambia la relación comercial. El cambio de
Taller comercial ocurre cuando la Flota deja de operar con el anterior, pero
continúa pagando y utilizando AutoCam.

## AUDIT_TRAIL

Nombre

Trazabilidad y auditoría de decisiones

Estado

Pendiente

Prioridad

P0

Descripción

Conservar quién, cuándo y sobre qué hecho tomó decisiones relevantes de
aprobación, rechazo, cancelación, corrección, resolución y habilitación
operativa.

Valor para el negocio

Permite reconstruir decisiones ante reclamos comerciales, legales, de seguro
u operativos.

Dependencias

IDENTITY_ACCESS

Desbloquea

WORK_ORDERS, CHECKLISTS, ALERTS, PAYMENT_STATUS y la defensa histórica del
producto.

MVP

Sí

Observaciones

La evidencia de Checklist y las versiones de Escalón son inmutables. La
habilitación urgente de una reparación extra debe distinguirse de la
aprobación comercial de la Flota.

## WORKSHOP_BRANDING

Nombre

Identidad básica de marca blanca

Estado

Pendiente

Prioridad

P1

Descripción

Permitir que el Taller presente el servicio con su identidad básica, como
nombre, logo y colores.

Valor para el negocio

Refuerza el enfoque Taller-first y convierte AutoCam en un servicio propio del
Taller frente a sus Flotas clientes.

Dependencias

WORKSHOP_ONBOARDING, IDENTITY_ACCESS

Desbloquea

La presentación comercial de la plataforma por cada Taller.

MVP

Sí

Observaciones

V1 contempla identidad básica. Dominios, correos y experiencias completamente
independientes quedan fuera del MVP.

## Revisión final del backlog

- No se incluyeron historias de usuario, tareas, especificaciones ni
  decisiones técnicas.
- No se incluyeron Prisma, NestJS, Docker, API, frontend, backend, base de
  datos, testing ni CI/CD como Features.
- No hay Features duplicadas: identidad, pertenencia, operación, mantenimiento,
  alertas, relación comercial y auditoría tienen límites separados.
- Las capacidades amplias fueron divididas donde existe una responsabilidad de
  negocio independiente.
- Las dependencias siguen el orden de habilitación funcional.
- Todas las Features de V1 están marcadas como `MVP: Sí`; las capacidades
  explícitamente fuera de V1 no se incluyeron.
- La pasarela de pagos, facturación automática, telemetría, planes
  personalizados por Flota, multi-taller comercial real y app nativa no forman
  parte de este backlog V1.
- Las decisiones detalladas de cada Feature deberán validarse nuevamente en
  Constitution, Specification y Clarification antes de implementarse.
