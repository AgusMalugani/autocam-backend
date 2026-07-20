# Bounded Contexts de AutoCam

Estos límites representan modelos y responsabilidades del negocio. No son
una propuesta de módulos técnicos ni de despliegue.

## 1. Identidad y pertenencia

**Objetivo:** Determinar quién es una persona y en qué organización puede
actuar.

**Responsabilidades:**

- Identidad global de la persona.
- Pertenencia a un Taller o Flota.
- Roles válidos y combinación de roles.
- Alcance de autoridad dentro de un tenant.

**Entidades principales:** Usuario, Membresía, Taller, Flota.

**Relaciones:** Provee identidad y autorización conceptual a todos los demás
contextos. Consume cambios de alta, baja o pertenencia.

**Límites:** No decide salud del vehículo, mantenimiento, pagos ni ejecución
operativa.

**Eventos que produce:** Usuario incorporado, Membresía creada, Rol asignado,
Membresía retirada.

**Eventos que consume:** Invitación aceptada, Flota creada, Taller creado,
Invitación revocada.

**Reglas V1:** Admin del Taller invita cualquier rol. Dueño de Flota solo
invita choferes. Las invitaciones no expiran en V1. Cualquier Admin del Taller
o Dueño de Flota del tenant puede revocar una invitación pendiente.

## 2. Flota y activos

**Objetivo:** Gestionar la propiedad, identidad y estado base de vehículos y
cubiertas de una Flota.

**Responsabilidades:**

- Alta y ciclo de vida del vehículo.
- Propiedad del activo.
- Kilometraje vigente del vehículo.
- Identidad física e historial de cubiertas.
- Asignación temporal de cubiertas.
- Vencimientos que pueden bloquear la operación.

**Entidades principales:** Vehículo, Cubierta, Asignación de Cubierta,
Vencimiento, Flota.

**Relaciones:** Consume pertenencia de Identidad; provee vehículos a Campo,
Mantenimiento, Trabajos y Alertas.

**Límites:** No autoriza trabajos, no interpreta anomalías del Chofer como
alertas y no administra la suscripción.

**Eventos que produce:** Vehículo incorporado, Kilometraje actualizado,
Cubierta asignada, Cubierta retirada, Vencimiento registrado, Vehículo
bloqueado por vencimiento.

**Eventos que consume:** Checklist validado, Trabajo finalizado, Cambio de
Flota.

## 3. Operación de campo

**Objetivo:** Capturar evidencia diaria del estado del vehículo bajo
condiciones reales de ruta y conectividad.

**Responsabilidades:**

- Checklist diario.
- Reporte de anomalías.
- Captura de kilometraje.
- Registro de hora local y sincronización.
- Clasificación de datos tardíos, sospechosos o con reloj defectuoso.
- Corrección append-only de evidencia.

**Entidades principales:** Checklist, Observación de Campo, Corrección de
Checklist.

**Relaciones:** Consulta vehículo y Chofer; publica hechos para Alertas y
actualización de kilometraje en Flota y Activos.

**Límites:** No decide si una reparación se aprueba, no corrige
retroactivamente un checklist y no trata el reloj local como autoridad.

**Eventos que produce:** Checklist completado, Checklist sincronizado,
Anomalía reportada, Kilometraje reportado, Checklist marcado tardío,
Checklist corregido.

**Eventos que consume:** Chofer autorizado, Vehículo activo.

## 4. Mantenimiento preventivo

**Objetivo:** Definir qué mantenimiento corresponde según tipo de vehículo,
kilometraje y deuda histórica.

**Responsabilidades:**

- Planes generales por tipo de vehículo.
- Escalones vigentes e históricos.
- Evaluación de umbrales.
- Registro de cumplimiento.
- Arrastre y escalamiento de tareas no resueltas.

**Entidades principales:** Plan de Mantenimiento, Escalón de Mantenimiento,
Cumplimiento de Escalón, Deuda de Mantenimiento.

**Relaciones:** Consume kilometraje desde Flota y Activos; provee necesidades
de mantenimiento a Trabajos y hechos de riesgo a Alertas.

**Límites:** No personaliza planes por Flota en V1, no ejecuta reparaciones,
no aprueba gastos y no modifica alertas ya emitidas.

**Eventos que produce:** Escalón alcanzado, Mantenimiento requerido, Tarea
rechazada, Deuda arrastrada, Urgencia escalada, Cumplimiento registrado.

**Eventos que consume:** Kilometraje actualizado, Orden finalizada,
Decisión comercial de la Flota.

## 5. Ejecución y autorización de trabajos

**Objetivo:** Coordinar diagnóstico, propuesta, decisión comercial y ejecución
de trabajos sobre un vehículo.

**Responsabilidades:**

- Registrar diagnósticos técnicos con tareas sugeridas.
- Crear y avanzar órdenes de trabajo a partir del diagnóstico.
- Registrar quién ejecuta el trabajo.
- Separar ejecución técnica de decisión comercial.
- Obtener aprobación o rechazo por tarea de la Flota.
- Agregar reparaciones extras durante la ejecución, clasificándolas como
  menores o no menores.
- Marcar reparaciones extras no menores urgentes como habilitadas por urgencia
  del Taller, con confirmación explícita del Admin del Taller al cargar la tarea.
- Aplicar SLA y escalar órdenes vencidas.
- Registrar tareas realizadas, rechazadas, canceladas y operativamente postergadas.

**Entidades principales:** Diagnóstico, Orden de Trabajo, Tarea de Mantenimiento,
Reparación extra, Decisión de la Flota, Intervención del Taller.

**Relaciones:** Consume necesidades de Mantenimiento Preventivo y datos de
vehículos; produce cumplimientos y hechos para Alertas.

**Límites:** No es dueño del historial del vehículo, no administra cobros de
suscripción y no permite que el Mecánico rechace por el cliente.

**Eventos que produce:** Diagnóstico registrado, Orden creada, Tarea aprobada,
Tarea rechazada, Reparación extra agregada, Reparación extra habilitada por
urgencia, Orden vencida, Orden escalada, Trabajo iniciado, Trabajo finalizado,
Orden cancelada, Orden reabierta.

**Eventos que consume:** Mantenimiento requerido, Decisión de Flota,
Vencimiento bloqueante, Intervención del Admin.

## 6. Salud predictiva y alertas

**Objetivo:** Convertir hechos de campo, kilometraje, inactividad, vencimientos
y deuda de mantenimiento en riesgos visibles y auditables.

**Responsabilidades:**

- Evaluar reglas de desgaste, anomalía, inactividad, vencimiento y arrastre.
- Generar alertas congeladas.
- Clasificar severidad.
- Deduplicar y agrupar por vehículo.
- Marcar correcciones reportadas.
- Entregar notificaciones en plataforma y por email según severidad.
- Permitir resolución humana de alertas no críticas y críticas con auditoría.

**Entidades principales:** Alerta, Notificación, Regla de Salud, Severidad.

**Reglas de resolución V1:**

- `INFORMATIVA` y `URGENTE`: Admin del Taller, Mecánico o Dueño de Flota.
- `CRITICA`: Admin del Taller, Mecánico o Dueño de Flota, con auditoría
  obligatoria. Nunca se autorresuelve.

**Relaciones:** Consume hechos de Campo, Flota y Activos, Mantenimiento y
Trabajos. Publica alertas para los actores responsables.

**Límites:** No cambia la evidencia de origen, no auto-resuelve alertas
críticas y no reemplaza la decisión humana.

**Eventos que produce:** Alerta generada, Alerta agrupada, Notificación
enviada, Corrección reportada, Alerta resuelta por humano.

**Eventos que consume:** Checklist con anomalía, Kilometraje actualizado,
Vehículo inactivo, Vencimiento próximo, Deuda escalada.

## 7. Relación comercial y ciclo de pago

**Objetivo:** Gestionar la relación de suscripción entre Taller y Flota y
proteger los datos durante mora, bloqueo, suspensión y purga.

**Responsabilidades:**

- Determinar quién es el Taller comercial.
- Cambiar `tallerComercialId` solo cuando la Flota deja al Taller pero sigue
  pagando AutoCam directamente.
- Conservar membresía y acceso aunque la Flota repare en otro taller ejecutor.
- Reflejar manualmente el estado de pago en V1.
- Aplicar acceso completo, solo lectura o suspensión.
- Controlar la oportunidad de exportación.
- Ejecutar la retención previa a purga.

**Entidades principales:** Relación Comercial, Estado de Pago, Suscripción,
Historial de Estado, Exportación de Historial.

**Relaciones:** Consume pertenencia y relación Taller-Flota; limita acceso a
Identidad, Flota, Campo, Trabajos y Alertas.

**Límites:** No implementa pasarela ni facturación automática en V1. No borra
datos antes del ciclo completo.

**Eventos que produce:** Flota en mora, Flota bloqueada, Flota suspendida,
Flota regularizada, Datos purgados.

**Eventos que consume:** Cambio de Taller comercial, Toggle de estado de pago,
Solicitud de exportación, Paso del tiempo.

## Relaciones entre contextos

```text
Identidad y pertenencia
          ↓
Flota y activos ─────→ Operación de campo
      ↓                      ↓
Mantenimiento preventivo ← kilometraje/anomalías
      ↓
Ejecución y autorización de trabajos
      ↓
Salud predictiva y alertas

Relación comercial y ciclo de pago limita el acceso transversal,
pero no se apropia de los datos operativos.
```
