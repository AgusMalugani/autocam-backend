# AutoCam — Definición de Producto

## 1. Resumen

AutoCam es una plataforma SaaS B2B2B de marca blanca que ayuda a talleres
mecánicos a gestionar el mantenimiento de las flotas de sus clientes.

El taller es el cliente directo y el centro de la propuesta comercial.
Las empresas de transporte, los mecánicos y los choferes participan en una
misma fuente de información para anticipar mantenimientos, reducir
imprevistos y conservar la historia completa de cada vehículo.

AutoCam no busca solamente digitalizar planillas. Busca transformar un modelo
de mantenimiento reactivo —usar el vehículo hasta que se rompe— en uno
preventivo y, progresivamente, predictivo.

## 2. Problema

El mantenimiento de flotas de transporte pesado se gestiona habitualmente con
audios de WhatsApp, papeles y planillas desactualizadas. Esto provoca:

- reparaciones reactivas y camiones detenidos de forma imprevista;
- falta de contexto entre el chofer, la flota y el taller;
- diagnósticos repetidos y pérdida de tiempo operativo;
- aprobaciones de reparaciones demoradas o sin responsables claros;
- vencimientos, controles y tareas de mantenimiento olvidados;
- historiales fragmentados o perdidos al cambiar de taller;
- poca previsibilidad comercial y operativa para el taller;
- información de campo que se pierde cuando el chofer no tiene conexión.

El resultado es mayor downtime para la flota, urgencias para el taller y una
relación comercial sostenida por información incompleta.

## 3. Propuesta de valor

### Para el taller

AutoCam le permite anticipar trabajos, ordenar su operación y fidelizar a sus
clientes corporativos mediante un servicio recurrente con su propia identidad.
El taller obtiene visibilidad sobre el estado de las flotas, las próximas
necesidades de mantenimiento y las órdenes que requieren intervención.

### Para la empresa de transporte

La flota obtiene una visión centralizada de sus vehículos, vencimientos,
reparaciones, cubiertas y tareas pendientes. Conserva la propiedad y
continuidad del historial de cada vehículo, incluso si cambia el taller que
ejecuta una reparación.

### Para el mecánico

El mecánico recibe contexto antes de inspeccionar el vehículo, registra su
diagnóstico y documenta el trabajo realizado. Puede concentrarse en decisiones
técnicas sin asumir decisiones comerciales que corresponden al cliente.

### Para el chofer

El chofer dispone de un canal simple y confiable para informar kilometraje y
anomalías, incluso sin conexión. Las limitaciones de señal, del dispositivo o
del odómetro no deben provocar la pérdida de su reporte.

## 4. Usuarios

### Cliente directo

**Dueño o administrador del taller.** Contrata AutoCam, incorpora empresas de
transporte y ofrece la plataforma como parte de su servicio.

### Usuarios operativos

- **Dueño de flota:** administra vehículos y choferes, controla el estado de
  la flota y decide sobre gastos de mantenimiento.
- **Mecánico:** diagnostica, propone tareas y registra trabajos realizados.
- **Chofer:** completa el checklist diario y reporta anomalías en ruta.

Una misma persona puede cumplir más de un rol cuando la operación lo requiera.

## 5. Posicionamiento

AutoCam adopta un enfoque **taller-first**.

El producto se diseña y comercializa desde las necesidades del taller:
previsibilidad, fidelización, orden operativo e ingresos recurrentes. Esto no
reduce la importancia de la flota ni del chofer; su adopción y sus resultados
son indispensables para que el taller reciba valor.

El modelo comercial es:

1. AutoCam vende la plataforma al taller.
2. El taller incorpora a sus empresas de transporte.
3. Las flotas gestionan sus vehículos y choferes.
4. Todos colaboran sobre el mismo historial operativo.

## 6. MVP — AutoCam V1

El MVP corresponde al alcance completo de AutoCam V1. Su objetivo es validar
que un taller pagará de forma recurrente por una plataforma que le permite
incorporar y atender mejor a sus flotas clientes.

### Flujo principal

1. El chofer completa un checklist diario, aun sin conexión.
2. Una anomalía se comunica a la flota y al taller.
3. El mecánico recibe el contexto, diagnostica y propone tareas.
4. Se genera una orden de trabajo.
5. El dueño de flota aprueba o rechaza las tareas comerciales.
6. El taller ejecuta y registra el trabajo.
7. El resultado pasa a formar parte del historial permanente del vehículo.

### Funcionalidades imprescindibles

- gestión de talleres, flotas, usuarios, vehículos y roles;
- invitación y alta de empresas de transporte;
- identidad básica de marca blanca por taller: nombre, logo y colores;
- checklist diario offline-first con sincronización posterior;
- reporte de anomalías y kilometraje;
- diagnóstico del mecánico y órdenes de trabajo;
- aprobación o rechazo de tareas con responsabilidades claras;
- escalamiento de órdenes sin respuesta, sin aprobación automática;
- historial inmutable de checklists, decisiones y mantenimientos;
- plan de mantenimiento escalonado por tipo de vehículo;
- arrastre y escalamiento de tareas rechazadas no resueltas;
- alertas de anomalías, desgaste e inactividad agrupadas por vehículo;
- control de seguros, VTV y otros vencimientos;
- pasaporte individual de cubiertas: posición, kilometraje y recapados;
- visualización del estado operativo de cada flota;
- gestión manual del estado de pago de las flotas;
- conservación y exportación del historial antes de una eventual purga.

## 7. Funcionalidades que pueden esperar

Quedan fuera del MVP:

- pasarela de pago y facturación automática;
- operación comercial con múltiples talleres reales;
- planes de mantenimiento personalizados para una flota particular;
- aplicación móvil nativa;
- personalización avanzada de marca blanca, como dominio, correos y
  experiencia completamente independientes;
- telemetría mediante sensores o integración directa con los vehículos;
- expansión internacional.

La arquitectura puede preparar estas capacidades, pero no deben retrasar la
validación comercial de V1.

## 8. Diferenciadores

- **Taller-first:** convierte la plataforma en una herramienta comercial y
  operativa del taller, no solamente en otra aplicación para flotas.
- **Marca blanca:** permite que cada taller presente el servicio con su
  identidad básica.
- **Offline-first:** el reporte del chofer no depende de tener señal.
- **Historial propiedad de la flota:** la información acompaña al vehículo y
  no queda cautiva del taller.
- **Trazabilidad:** checklists, decisiones y reglas históricas permanecen
  auditables.
- **Mantenimiento progresivamente predictivo:** los datos diarios permiten
  anticipar servicios sin exigir telemetría costosa durante el MVP.
- **Separación de responsabilidades:** las decisiones técnicas, operativas y
  comerciales pertenecen a actores claramente definidos.

## 9. Validación del MVP

La señal principal de validación será que talleres estén dispuestos a pagar
de forma recurrente y logren incorporar activamente a sus flotas clientes.

No se fija todavía una cantidad mínima de talleres. El piloto deberá producir
información suficiente para establecer una meta comercial realista.

Se observarán además estas señales complementarias:

- las flotas incorporadas usan el producto de forma sostenida;
- los choferes completan checklists con regularidad;
- las órdenes recorren el flujo completo con menos dependencia de mensajes,
  papeles o planillas;
- el taller puede anticipar trabajos en lugar de recibir únicamente urgencias;
- disminuyen los mantenimientos omitidos y los períodos imprevistos fuera de
  servicio.

## 10. Visión a dos años

En dos años, AutoCam busca convertirse en una **red multi-taller consolidada
en Argentina para el mantenimiento coordinado de flotas**.

En esa red:

- distintos talleres podrán ofrecer AutoCam con su propia identidad;
- una flota podrá operar con múltiples talleres sin fragmentar sus datos;
- cada vehículo conservará una historia de mantenimiento única y portable;
- los talleres podrán anticipar demanda y ofrecer servicios recurrentes;
- el historial acumulado mejorará la capacidad de prevenir fallas;
- AutoCam funcionará como infraestructura compartida entre talleres, flotas,
  mecánicos y choferes.

La visión no es crear un directorio de talleres. Es construir una red operativa
con datos confiables, continuidad del historial y reglas comunes de
mantenimiento.

## 11. Límites y principios de producto

- El taller es el cliente directo, pero no es propietario de los datos de la
  flota.
- El dueño de flota conserva la decisión comercial sobre sus reparaciones.
- Una alerta crítica requiere revisión humana y no se cierra automáticamente.
- La falta de conexión nunca debe impedir que el chofer registre información.
- Una demora de aprobación no puede bloquear indefinidamente una orden.
- El crecimiento futuro no debe comprometer el aislamiento entre
  organizaciones.
- Las funcionalidades futuras no deben ampliar el MVP sin una decisión
  explícita de producto.

## 12. Supuestos

- AutoCam comienza con un único taller real que funciona como piloto.
- El cobro de suscripciones se realiza fuera de la plataforma durante V1.
- Los talleres son el canal de adquisición de las flotas.
- La PWA cubre las necesidades móviles iniciales del chofer.
- El kilometraje y los checklists aportan valor preventivo antes de incorporar
  telemetría.
- Las metas numéricas de adopción, retención y reducción de downtime se
  definirán después de obtener datos del piloto.
