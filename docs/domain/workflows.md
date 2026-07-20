# Workflows de negocio de AutoCam

Los workflows describen comportamiento y decisiones del dominio, no pantallas,
endpoints ni implementación.

## Alta de Flota

**Objetivo:** Incorporar una empresa de transporte al ecosistema del Taller.

**Actor inicial:** `ADMIN_TALLER`.

**Evento inicial:** El Taller decide incorporar una nueva Flota.

**Secuencia:**

1. El Taller identifica la empresa.
2. Se crea la relación comercial inicial.
3. La Flota queda en estado `AL_DIA`.
4. Se incorpora el representante con el rol correspondiente.
5. Se habilita la gestión de vehículos y choferes.

**Resultado esperado:** Flota activa, con propietario de datos y relación
comercial identificados.

**Reglas:** La Flota es dueña de sus vehículos; el Taller no absorbe ese
historial.

**Excepciones:** Datos incompletos, invitación no aceptada o Flota ya existente.

## Alta de Vehículo

**Objetivo:** Incorporar un activo a una Flota.

**Actor inicial:** Dueño de Flota.

**Evento inicial:** Solicitud de incorporación de un vehículo.

**Secuencia:**

1. Se identifica el vehículo por patente.
2. Se registra la Flota propietaria y el tipo de vehículo.
3. Se establece el kilometraje inicial conocido.
4. Se registran datos opcionales del activo.
5. El vehículo queda disponible para checklist, vencimientos y mantenimiento.

**Resultado esperado:** Vehículo operativo dentro de una única Flota.

**Reglas:** La patente identifica una unidad; el Taller no es propietario.

**Excepciones:** Patente duplicada, Flota suspendida o datos insuficientes.

## Invitación de Usuarios

**Objetivo:** Conceder pertenencia a un Taller o Flota.

**Actor inicial:** Admin del Taller o Dueño de Flota.

**Evento inicial:** Invitación emitida.

**Secuencia:**

1. El invitador identifica al usuario y el rol permitido.
2. Se emite una invitación.
3. El usuario acepta o rechaza.
4. Si acepta, se crea la Membresía con el rol permitido.
5. El usuario puede actuar dentro del tenant.
6. Cualquier Admin del Taller o Dueño de Flota del tenant puede revocar una
   invitación pendiente antes de su aceptación.

**Resultado esperado:** Usuario incorporado con autoridad delimitada.

**Reglas:** Admin del Taller invita cualquier rol. Dueño de Flota solo invita
choferes. Las invitaciones no expiran en V1. Cualquier Admin del Taller o
Dueño de Flota del tenant puede revocar invitaciones pendientes. Los roles
válidos son solo `ADMIN_TALLER`, `MECANICO`, `DUENIO_FLOTA` y `CHOFER`.

**Excepciones:** Invitación rechazada, revocada o rol no válido.

## Checklist Diario

**Objetivo:** Obtener evidencia diaria del estado del vehículo.

**Actor inicial:** Chofer.

**Evento inicial:** Inicio de la inspección diaria.

**Secuencia:**

1. El Chofer observa frenos, luces, aceite y posibles anomalías.
2. Registra kilometraje, salvo que el odómetro esté inoperativo.
3. El reporte se conserva localmente si no existe conectividad.
4. Al recuperar conectividad, se sincroniza.
5. Se compara el kilometraje con el vigente del vehículo.
6. Se detectan saltos sospechosos sin bloquear el reporte.
7. Se evalúa reloj local frente al reloj de sincronización.
8. Se clasifica como `A_TIEMPO` o `TARDIO_VALIDAR`.
9. Si corresponde, se publica una anomalía para el motor de alertas.
10. Si se necesita corregir, se crea un nuevo checklist relacionado.

**Resultado esperado:** Evidencia conservada, kilometraje válido actualizado y
anomalías encaminadas a revisión o alerta.

**Reglas:** El checklist es inmutable; `timestampLocal` no decide la ventana;
un checklist tardío no dispara automáticamente la alerta de anomalía.

**Excepciones:** Odómetro inoperativo, reloj sospechoso, kilometraje menor o
salto anormal.

## Corrección de Checklist

**Objetivo:** Corregir una observación sin perder el hecho original.

**Actor inicial:** Chofer o usuario autorizado.

**Evento inicial:** Se detecta un dato incorrecto.

**Secuencia:**

1. Se identifica el checklist original.
2. Se crea un nuevo checklist con el dato corregido.
3. El nuevo hecho referencia al anterior.
4. Se conserva la cadena de evidencia.
5. Si había alerta crítica, se marca corrección reportada.
6. Un humano decide si la alerta debe cerrarse.

**Resultado esperado:** Corrección trazable sin autorresolución crítica.

**Reglas:** Nunca se edita ni borra el checklist anterior.

**Excepciones:** Referencia inexistente o corrección que llega fuera de ventana.

## Motor Predictivo

**Objetivo:** Identificar mantenimiento o riesgo antes de una falla.

**Actor inicial:** Sistema de negocio ante un hecho nuevo o una evaluación
periódica.

**Evento inicial:** Kilometraje actualizado, anomalía, vencimiento, inactividad
o cambio de deuda.

**Secuencia:**

1. Se toma el estado vigente del vehículo.
2. Se evalúan umbrales del plan aplicable.
3. Se consideran tareas arrastradas.
4. Se evalúan anomalías y vencimientos.
5. Se ejecuta la regla de inactividad de 7 días.
6. Se determina tipo y severidad del riesgo.
7. Se genera o actualiza la representación de alerta sin reescribir
   evidencia histórica.
8. Se agrupan alertas del mismo vehículo.
9. Se notifica en plataforma y por email según severidad a Dueño de Flota y
   Admin del Taller.

**Resultado esperado:** Riesgos persistidos, deduplicados y priorizados.

**Reglas:** Cambios de plan no son retroactivos; alertas críticas no se
autorresuelven.

**Excepciones:** Checklist tardío, reloj sospechoso, dato de kilometraje
sospechoso o falta de información.

## Generación y Resolución de Alertas

**Objetivo:** Convertir una condición de riesgo en una decisión humana
trazable.

**Actor inicial:** Motor Predictivo.

**Evento inicial:** Regla de alerta satisfecha.

**Secuencia:**

1. El motor identifica vehículo, tipo y severidad.
2. Persiste una alerta congelada con su mensaje y origen.
3. Agrupa alertas del vehículo.
4. Notifica en plataforma y por email según severidad.
5. Un usuario autorizado revisa la condición.
6. Si llega una corrección, se marca `correccionReportada`.
7. Un humano autorizado resuelve o mantiene abierta la alerta.

**Resultado esperado:** Riesgo visible, notificado y decisión auditable.

**Reglas:** Una alerta crítica no se cierra por recibir una corrección.
`INFORMATIVA` y `URGENTE` pueden resolverse por Admin del Taller, Mecánico o
Dueño de Flota. `CRITICA` puede cerrarse manualmente por esos mismos roles con
auditoría obligatoria.

**Excepciones:** Duplicado, corrección contradictoria o condición que ya no
puede evaluarse.

## Mantenimiento Escalonado

**Objetivo:** Determinar y conservar el mantenimiento esperado por kilometraje.

**Actor inicial:** Motor Predictivo o Taller.

**Evento inicial:** Vehículo alcanza un umbral del plan.

**Secuencia:**

1. Se identifica el plan por tipo de vehículo.
2. Se selecciona la versión vigente para el cálculo.
3. Se identifican tareas del escalón.
4. Se ejecutan o proponen las tareas mediante una Orden de Trabajo.
5. Se registra cumplimiento.
6. Se registran tareas rechazadas o impedidas.
7. Las tareas no resueltas se convierten en deuda arrastrada.
8. La deuda escala su urgencia al superar el umbral correspondiente.

**Resultado esperado:** Estado real del mantenimiento y deuda histórica
explicable.

**Reglas:** Solo el Dueño de Flota rechaza por decisión comercial; las
versiones de escalón son inmutables.

**Excepciones:** Falta de stock, postergación del Taller, rechazo del cliente,
vehículo sin tipo compatible.

## Diagnóstico y creación de Orden de Trabajo

**Objetivo:** Transformar una inspección técnica en una orden ejecutable.

**Actor inicial:** Mecánico.

**Evento inicial:** Vehículo ingresado o necesidad técnica detectada.

**Secuencia:**

1. El Mecánico inspecciona el vehículo.
2. Registra un Diagnóstico con tareas sugeridas.
3. Se identifica el Taller ejecutor.
4. Se crea la Orden de Trabajo a partir del diagnóstico.
5. La orden incluye observación de que pueden surgir reparaciones extras.
6. Se establece el SLA de respuesta.
7. La orden queda `PENDIENTE` hasta decisiones comerciales por tarea.

**Resultado esperado:** Orden creada con base técnica trazable.

**Reglas:** El diagnóstico no implica aprobación comercial.

**Excepciones:** Vehículo bloqueado por vencimiento o datos insuficientes.

## Orden de Trabajo

**Objetivo:** Coordinar una intervención de mantenimiento sobre un vehículo.

**Actor inicial:** Mecánico o Taller.

**Evento inicial:** Diagnóstico registrado o escalón de mantenimiento alcanzado.

**Secuencia:**

1. Se identifica el vehículo y las tareas del diagnóstico o escalón.
2. Se identifica el Taller ejecutor.
3. Se crea la orden en `PENDIENTE`.
4. Se establece el plazo de respuesta.
5. La Flota aprueba o rechaza **por tarea**.
6. Las tareas aprobadas habilitan ejecución parcial o total.
7. Las tareas rechazadas pueden generar deuda de mantenimiento.
8. Si no hay respuesta a tiempo, la orden pasa a `VENCIDA`.
9. El Admin del Taller interviene manualmente sin autoaprobar.
10. Durante `EN_PROGRESO`, Mecánico o Admin agregan reparaciones extras y las
    clasifican como menores o no menores.
11. Las extras menores se registran sin notificación formal.
12. Las extras no menores se notifican al Admin del Taller y al Dueño de Flota.
13. Si una extra no menor es urgente, el Admin del Taller la marca como
    habilitada por urgencia del Taller con modal y registro de auditoría.
14. El trabajo se registra como finalizado, cancelado o reabierto como nueva
    orden vinculada.

**Resultado esperado:** Trabajo ejecutado con decisiones comerciales por tarea
y trazabilidad de extras.

**Reglas:** Vencimiento no implica aprobación; el Taller comercial y el
ejecutor pueden ser diferentes.

**Excepciones:** Cliente no responde, falta de stock, trabajo cancelado,
Taller externo o vencimiento bloqueante.

## Cancelación de Orden de Trabajo

**Objetivo:** Interrumpir una orden antes de su cierre normal.

**Actor inicial:** Admin del Taller, Mecánico o Dueño de Flota, según estado.

**Evento inicial:** Decisión de cancelar la orden.

**Secuencia:**

1. Se identifica la orden y su estado vigente.
2. Un actor autorizado solicita la cancelación.
3. Se registra motivo y responsable.
4. La orden pasa a `CANCELADA`.
5. Si corresponde, puede reabrirse como nueva orden vinculada.

**Resultado esperado:** Cierre anticipado trazable sin perder historial.

**Reglas:** Cancelar no equivale a rechazo comercial por tarea.

**Excepciones:** Orden ya finalizada o actor sin autoridad para el estado actual.

## Reparaciones extras durante ejecución

**Objetivo:** Registrar y gestionar trabajo adicional descubierto en el camino.

**Actor inicial:** Mecánico o Admin del Taller.

**Evento inicial:** Hallazgo de trabajo adicional durante `EN_PROGRESO`.

**Secuencia:**

1. Se identifica la orden en ejecución.
2. Mecánico o Admin agrega la reparación extra.
3. Clasifica la tarea como menor o no menor.
4. Si es menor, queda registrada sin notificación formal.
5. Si no es menor, se notifica al Admin del Taller y al Dueño de Flota.
6. Si además es urgente, el Admin del Taller confirma con modal y marca la tarea
   como habilitada por urgencia del Taller (`habilitadaPorId`, `modalConfirmadoAt`).
7. La reparación extra queda trazada en el historial del vehículo.

**Resultado esperado:** Trabajo adicional registrado con el nivel correcto de
control y trazabilidad.

**Reglas:** La clasificación menor/no menor es criterio del Taller al registrar.
Habilitar por urgencia no reemplaza al Dueño de Flota; es habilitación
operativa del Taller con evidencia explícita.

**Excepciones:** Orden no está en ejecución o actor sin autoridad.

## Aprobación de Reparaciones

**Objetivo:** Obtener una decisión comercial explícita sobre el gasto.

**Actor inicial:** Dueño de Flota.

**Evento inicial:** Orden pendiente presentada para decisión.

**Secuencia:**

1. La Flota revisa las tareas propuestas.
2. Decide aprobar o rechazar **cada tarea**.
3. Si aprueba una tarea, confirma explícitamente la decisión.
4. Se registra quién decidió y cuándo.
5. Si rechaza una tarea, se registra el motivo controlado.
6. La tarea rechazada puede ingresar a deuda de mantenimiento.
7. La orden puede avanzar con las tareas aprobadas aunque otras sigan
   pendientes o rechazadas.

**Resultado esperado:** Decisiones comerciales granulares y atribuibles.

**Reglas:** Mecánico y Admin no sustituyen la decisión del cliente; la
intervención por SLA vencido tampoco autoaprueba.

**Excepciones:** Orden vencida o usuario sin autoridad.

## Reparación en otro Taller ejecutor

**Objetivo:** Registrar trabajo realizado fuera del Taller comercial sin
alterar la relación comercial de la Flota.

**Actor inicial:** Taller, Mecánico o Admin del Taller.

**Evento inicial:** Decisión de ejecutar una orden en otro taller o taller
externo.

**Secuencia:**

1. Se mantiene `tallerComercialId` sin cambios.
2. Se crea o actualiza la Orden de Trabajo con el Taller ejecutor correspondiente.
3. Se registra si el ejecutor es externo.
4. El historial del vehículo incorpora el trabajo realizado.
5. La Flota conserva su membresía y acceso a la plataforma.

**Resultado esperado:** Historial completo sin fragmentar la Flota ni cambiar
su relación comercial.

**Reglas:** Ir a reparar a otro taller no implica cambio de Taller comercial.

**Excepciones:** Flota suspendida o falta de permisos operativos.

## Cambio de Taller comercial

**Objetivo:** Actualizar quién mantiene la relación comercial de suscripción
cuando la Flota deja de operar con su Taller anterior pero sigue usando
AutoCam.

**Actor inicial:** Acuerdo entre Flota y plataforma/Taller según operación
comercial.

**Evento inicial:** La Flota deja al Taller comercial anterior pero continúa
pagando AutoCam directamente.

**Secuencia:**

1. Se valida la continuidad del servicio y la membresía.
2. Se actualiza `tallerComercialId` al nuevo responsable comercial acordado.
3. Se conservan vehículos, checklists, alertas e historial.
4. La Flota mantiene acceso según su estado de pago.

**Resultado esperado:** Nueva relación comercial sin pérdida de historial.

**Reglas:** Este cambio no ocurre por reparar en otro taller ejecutor.

**Excepciones:** Mora, suspensión, purga en curso o falta de acuerdo comercial.

## Cambio de Estado de Pago

**Objetivo:** Aplicar el ciclo de acceso y protección de datos ante mora.

**Actor inicial:** Admin del Taller en V1; cron de purga para la purga final.

**Evento inicial:** Toggle manual o paso del tiempo de retención.

**Secuencia:**

1. Se registra el nuevo estado.
2. `EN_MORA` mantiene acceso completo y avisos.
3. `BLOQUEADA` permite solo lectura y exportación.
4. `SUSPENDIDA` quita acceso operativo e inicia retención de seis meses.
5. Si se regulariza desde `SUSPENDIDA`, el Admin del Taller pasa manualmente
   la Flota a `AL_DIA`, restaura acceso completo y detiene el conteo de purga.
6. Si pasan 180 días desde suspensión sin regularizar, se ejecuta la purga.

**Resultado esperado:** Acceso coherente con pago sin pérdida prematura de datos.

**Reglas:** Transiciones manuales en V1; la purga respeta la oportunidad de
exportación y el ciclo completo.

**Excepciones:** Estado inconsistente, fecha de suspensión ausente o
regularización durante la retención.
