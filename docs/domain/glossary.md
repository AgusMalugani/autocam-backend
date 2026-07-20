# Glosario de negocio de AutoCam

Diccionario de referencia rápida. Las definiciones detalladas y las
restricciones semánticas están en `ubiquitous-language.md`.

| Término | Definición | Contexto | Conceptos relacionados |
|---|---|---|---|
| AutoCam | Plataforma SaaS B2B2B de mantenimiento de flotas. | Producto | Taller, Flota |
| Taller | Organización que comercializa y/o ejecuta mantenimiento. | Identidad, Trabajos | Taller comercial, Taller ejecutor |
| Flota | Empresa dueña de vehículos e historial. | Flota y activos | Vehículo, Dueño de Flota |
| Dueño de Flota | Autoridad comercial del cliente. | Trabajos | Aprobación, Rechazo |
| Admin del Taller | Rol administrativo del Taller. | Identidad, Relación comercial | SLA, Estado de pago |
| Mecánico | Rol técnico que diagnostica y ejecuta. | Trabajos | Tarea, Orden de Trabajo |
| Chofer | Rol de campo que reporta el vehículo. | Operación de campo | Checklist, Kilometraje |
| Vehículo | Activo de una Flota identificado por patente. | Flota y activos | Kilometraje, Vencimiento |
| Patente | Identificador operativo del vehículo. | Flota y activos | Vehículo |
| Kilometraje | Medida acumulada usada por mantenimiento. | Activos, Predictivo | `kmActual`, Escalón |
| Kilometraje sospechoso | Reporte aceptado pendiente de revisión por salto anormal. | Campo | Checklist |
| Odómetro inoperativo | Condición que permite omitir kilometraje sin bloquear checklist. | Campo | Chofer |
| Checklist | Evidencia diaria inmutable del Chofer. | Operación de campo | Anomalía, Sincronización |
| Checklist tardío | Checklist fuera de 48 horas o con reloj sospechoso. | Operación de campo | `TARDIO_VALIDAR` |
| Anomalía | Observación negativa o fuera de norma sobre el vehículo. | Campo, Alertas | Alerta crítica |
| Corrección | Nuevo hecho que rectifica un checklist anterior. | Campo, Auditoría | `supersedesId` |
| Timestamp local | Hora declarada por el dispositivo. | Campo | Reloj sospechoso |
| Timestamp de sincronización | Hora del servidor al recibir el checklist. | Campo | Ventana offline |
| Ventana offline | Máximo de 48 horas considerado a tiempo. | Campo | Sincronización |
| Reloj sospechoso | Dispositivo adelantado más de 10 minutos o atrasado más de 30 días. | Campo | Checklist tardío |
| Sincronización | Recepción server-side de evidencia creada en campo. | Campo | A tiempo, Tardío |
| Plan de Mantenimiento | Plantilla general por tipo de vehículo y Taller. | Mantenimiento | Escalón |
| Escalón | Versión de regla activada por kilometraje. | Mantenimiento | Umbral, Tareas |
| Umbral | Kilometraje que activa un escalón o escalamiento. | Mantenimiento | Plan |
| Tarea de mantenimiento | Unidad de trabajo técnico dentro de diagnóstico, escalón u orden. | Mantenimiento, Trabajos | Aprobación, Rechazo |
| Cumplimiento | Registro de lo realizado o no realizado en un escalón. | Mantenimiento | Deuda |
| Rechazo comercial | Decisión de la Flota de no autorizar una tarea. | Trabajos | Dueño de Flota |
| Falta de stock | Impedimento operativo del Taller. | Trabajos | Mecánico |
| Postergación del Taller | Demora solicitada por el Taller, no por el cliente. | Trabajos | Mecánico |
| Deuda de mantenimiento | Tarea no resuelta que continúa pendiente. | Mantenimiento | Arrastre |
| Arrastre | Traslado de deuda a un escalón posterior. | Mantenimiento | Urgencia |
| Urgencia | Severidad operativa de una deuda arrastrada. | Mantenimiento, Alertas | Informativa, Urgente |
| Orden de Trabajo | Unidad de trabajo técnico y decisión comercial. | Trabajos | SLA, Aprobación |
| Aprobación | Decisión explícita de autorizar una tarea dentro de una orden. | Trabajos | Dueño de Flota, Tarea |
| SLA | Plazo de respuesta de una Orden pendiente. | Trabajos | Vencida |
| Orden vencida | Orden sin respuesta dentro de su SLA. | Trabajos | Admin del Taller |
| Intervención manual | Acción humana tras una escalación. | Trabajos | No autoaprobación |
| Taller comercial | Taller que cobra la suscripción. | Relación comercial | Flota |
| Taller ejecutor | Taller que realiza un trabajo puntual. | Trabajos | Orden de Trabajo |
| Taller externo | Ejecutor no afiliado al Taller comercial. | Trabajos | Historial |
| Relación comercial | Vínculo de suscripción entre Taller y Flota. | Relación comercial | Estado de pago |
| Estado de pago | Estado de acceso por situación de suscripción. | Relación comercial | Mora, Bloqueo |
| Al día | Estado de pago normal. | Pago | Flota |
| En mora | Días 0–30 de atraso, con acceso completo. | Pago | Aviso |
| Bloqueada | Días 31–60, solo lectura y exportación. | Pago | Historial |
| Suspendida | Estado sin acceso operativo que inicia retención. | Pago | Purga |
| Purga | Borrado definitivo tras 180 días de suspensión. | Pago | Retención |
| Retención | Período de seis meses antes de purgar datos. | Pago | Exportación |
| Exportación | Oportunidad de obtener historial antes de perder acceso. | Pago | PDF |
| Cubierta | Unidad física con historial propio. | Flota y activos | Recapado |
| Recapado | Renovación de uso de una cubierta. | Flota y activos | Veces recapada |
| Asignación de cubierta | Uso temporal de una cubierta en vehículo y posición. | Flota y activos | `kmInicio`, `kmFin` |
| Posición | Ubicación física de la cubierta en el vehículo. | Flota y activos | Rotación |
| Vencimiento | Fecha límite de VTV, seguro u otro documento. | Flota y activos | Bloqueo |
| Bloqueo de asignación | Restricción de uso por vencimiento. | Flota y activos | Vehículo |
| Alerta | Riesgo persistido generado por una regla. | Salud predictiva | Severidad |
| Alerta informativa | Riesgo de baja urgencia. | Salud predictiva | Desgaste |
| Alerta urgente | Riesgo que requiere atención prioritaria. | Salud predictiva | Arrastre |
| Alerta crítica | Riesgo de alta gravedad que no se autorresuelve. | Salud predictiva | Anomalía |
| Corrección reportada | Indicador de que llegó una corrección relacionada con alerta. | Alertas | Auditoría humana |
| Deduplicación | Agrupación de alertas del mismo vehículo antes de notificar. | Alertas | Vehículo |
| Inactividad | Ausencia de reporte de un vehículo operativo durante 7 días. | Alertas | Estado desconocido |
| Estado desconocido | Alerta derivada de inactividad prolongada. | Alertas | `INACTIVIDAD_7D` |
| Inmutabilidad | Conservación de hechos y reglas sin sobrescritura. | Auditoría | Checklist, Escalón |
| Append-only | Corrección mediante nuevos hechos relacionados. | Auditoría | Historial |
| Historial | Conjunto de hechos de mantenimiento del vehículo. | Flota | Taller ejecutor |
| Tenant | Organización aislada dentro del producto. | Identidad | Taller, Flota |
| Membresía | Pertenencia de usuario a un tenant con roles. | Identidad | Usuario |
| Rol | Autoridad asignada dentro de una membresía. | Identidad | Admin, Mecánico |
| Diagnóstico | Inspección del Mecánico con tareas sugeridas. | Trabajos | Orden de Trabajo, Tarea |
| Reparación extra | Tarea agregada durante la ejecución fuera del diagnóstico inicial. | Trabajos | Orden de Trabajo |
| Reparación extra menor | Extra clasificada por el Taller como de bajo impacto; sin notificación formal. | Trabajos | Reparación extra |
| Habilitada por urgencia del Taller | Extra no menor urgente habilitada por Admin con modal al cargar la tarea. | Trabajos | Reparación extra, Modal |
| Orden cancelada | Estado terminal de una orden interrumpida antes de su cierre normal. | Trabajos | Reapertura |
| Notificación | Entrega de alerta o evento por plataforma y/o email. | Alertas | Alerta, Severidad |
| Invitación | Propuesta de incorporación sin expiración en V1. | Identidad | Membresía |
| Revocación de invitación | Anulación de invitación pendiente por Admin o Dueño de Flota del tenant. | Identidad | Invitación |

## Términos prohibidos o ambiguos

- “Cliente” sin aclarar si significa Flota, Dueño de Flota o Taller.
- “Taller de la Flota” cuando no se especifica si es comercial o ejecutor.
- “Aprobación automática” para una orden vencida.
- “Checklist inválido” para un checklist tardío o con kilometraje sospechoso.
- “Tarea rechazada” para falta de stock o postergación del Taller.
- “Notificación” como sinónimo de Alerta.
- “Presupuesto” como sinónimo de Orden de Trabajo o Diagnóstico.
- “Urgencia implícita” cuando falta confirmación explícita del Admin del Taller.
- “Aprobada por el taller” cuando se confunde con aprobación comercial de la Flota.
- “Editar historial” cuando la operación correcta es crear evidencia
  relacionada.
