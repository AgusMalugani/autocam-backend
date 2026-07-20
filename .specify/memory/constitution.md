# Constitución de AutoCam V1

> Constitución técnica y de dominio para el backend de AutoCam.  
> Las fuentes de negocio ratificadas se encuentran en `files/CONSTITUTION.md`,
> `files/product-analysis.md` y `docs/domain/`. Este documento define las
> restricciones de implementación y no reemplaza esas fuentes.

## 1. Pilares técnicos no negociables

### 1.1 Stack y runtime

- Runtime: Node.js 20 LTS.
- Framework: NestJS.
- Lenguaje: TypeScript 5.4 o superior.
- Compilación TypeScript: `strict: true`.
- `any` está prohibido bajo cualquier circunstancia.
- Package manager único: pnpm. No se permite npm ni yarn ni múltiples
  lockfiles.

### 1.2 Persistencia

- Base de datos: PostgreSQL 16.
- ORM: Prisma ORM.
- `schema.prisma` es la única fuente de verdad del esquema.
- Las migraciones se aplican mediante `prisma migrate deploy` en CI/CD.
- Desarrollo local: PostgreSQL aislado mediante `docker-compose.yml` con
  volumen local persistente.
- Staging y Producción: Neon PostgreSQL serverless.
- Toda conexión usa exclusivamente `DATABASE_URL`.
- El backend es la fuente de verdad de tiempo para `timestampSyncAt`.

### 1.3 Arquitectura

- Patrón: arquitectura modular propia de NestJS basada en DDD simplificado.
- La separación principal es por módulo de dominio:
  `src/modules/<dominio>`.
- Cada módulo separa estrictamente:
  - Controladores: transporte HTTP.
  - Servicios: lógica de negocio.
  - Repositorios: acceso a datos y Prisma.
- Los límites de negocio se basan en los bounded contexts documentados en
  `docs/domain/bounded-contexts.md`.
- No se mezclan reglas de dominio con controladores ni acceso directo a Prisma
  desde el transporte HTTP.

### 1.4 Estilo y naming

- Variables, funciones y métodos: `camelCase`.
- Clases, DTOs e interfaces: `PascalCase`.
- Constantes y enums: `UPPER_SNAKE_CASE`.
- Sustantivos de dominio, entidades y modelos: español:
  `Vehiculo`, `Taller`, `Flota`, `Checklist`.
- Verbos de acción: inglés:
  `Create`, `Update`, `Delete`, `Get`.
- Sufijos arquitectónicos: inglés:
  `Service`, `Controller`, `Module`, `Dto`, `Repository`.
- Ejemplos válidos: `CreateVehiculoDto`, `VehiculoService`,
  `FlotaController`, `UpdateChecklistStatusDto`.
- ESLint y Prettier son obligatorios.
- Los `switch` sobre unions o enums deben ser exhaustivos mediante una
  comprobación `never` en el caso por defecto.

### 1.5 Interfaces y contratos

- La interfaz externa es RESTful.
- La documentación de API se genera con Swagger/OpenAPI desde decoradores de
  NestJS.
- Los payloads usan JSON.
- La validación de entrada es estricta mediante DTOs, `class-validator`,
  `class-transformer` y `ValidationPipe` global.
- Las respuestas usan el envoltorio:

```json
{
  "data": "<payload>",
  "meta": "<paginacion_o_metadata>"
}
```

- `meta` es opcional.
- Los contratos deben respetar el lenguaje ubicuo y no exponer decisiones
  internas de persistencia.

### 1.6 Errores y seguridad

- La autenticación usa JWT administrado mediante Guards de NestJS.
- Toda consulta Prisma aplica automáticamente el tenant del usuario
  autenticado, usando `tallerComercialId` o `flotaId` según el contexto.
- Está prohibido devolver datos de otro tenant.
- Exception Filters globales estandarizan errores HTTP 400, 401, 403, 404 y
  500.
- Los stack traces están deshabilitados cuando `NODE_ENV=production`.
- Los secretos se inyectan mediante `@nestjs/config` y variables `.env`.
- Los secretos no se suben al repositorio.
- Las autorizaciones comerciales y operativas deben respetar los roles del
  dominio; el Admin del Taller no reemplaza la decisión comercial del Dueño
  de Flota.

### 1.7 Testing y calidad

- Framework de pruebas: Jest.
- Pruebas unitarias: lógica de dominio, servicios, reglas de desgaste y motor
  de alertas, aislando persistencia mediante mocks.
- Pruebas E2E: flujos críticos de API mediante Supertest.
- Cobertura mínima global: 70%.
- Cobertura mínima para autenticación y motor predictivo: 90%.
- Una Feature no se considera lista sin pruebas proporcionales a su riesgo.

### 1.8 Distribución y despliegue

- El backend se empaqueta en Docker.
- Las imágenes usan compilaciones multi-stage optimizadas para producción.
- La imagen de producción instala únicamente dependencias necesarias para
  ejecución.
- GitHub Actions es obligatorio para CI/CD.
- Pipeline mínimo:
  `Lint → Test → Build Docker → Deploy`.
- Staging y Producción se despliegan en Render.
- Merge a `main`: despliegue a Staging.
- Release tag: despliegue a Producción.
- La estrategia de trabajo es trunk-based development.

## 2. Principios de dominio

### 2.1 Propiedad del activo

El vehículo y su historial pertenecen a la Flota. El Taller ejecuta y factura,
pero no adquiere la propiedad del activo. `Flota.tallerComercialId` y
`OrdenTrabajo.tallerId` representan relaciones distintas.

### 2.2 Inmutabilidad histórica

- Los Checklists no se editan ni eliminan; una corrección crea un nuevo hecho
  relacionado.
- Las versiones de Escalón de Mantenimiento son append-only.
- Las alertas ya calculadas son congeladas y no se recalculan
  retroactivamente.

### 2.3 Decisión comercial

- Solo el Dueño de Flota puede rechazar una tarea por decisión comercial.
- Falta de stock y postergación del Taller son estados operativos, no rechazos
  del cliente.
- Una orden vencida nunca se autoaprueba.
- Una reparación extra no menor urgente puede ser habilitada operativamente
  por el Admin del Taller con confirmación explícita y auditoría, sin
  convertirse en aprobación comercial de la Flota.

### 2.4 Campo y tiempo

- `timestampSyncAt` es la referencia confiable del servidor.
- `timestampLocal` es informativo y no decide reglas de negocio.
- El kilometraje menor al vigente no reemplaza el valor actual.
- Un salto sospechoso se acepta para revisión; no se descarta el reporte.
- Un odómetro inoperativo no bloquea el checklist.

### 2.5 Alertas

- Las alertas se deduplican por vehículo antes de notificar.
- Una alerta crítica nunca se autorresuelve.
- Una corrección solo marca que existe una corrección reportada.
- El cierre de una alerta crítica requiere intervención humana y auditoría.

### 2.6 Retención y datos del cliente

La Flota atraviesa `AL_DIA → EN_MORA → BLOQUEADA → SUSPENDIDA` antes de una
eventual purga. La purga solo puede ocurrir después de seis meses desde la
suspensión y después de ofrecer exportación del historial.

## 3. Prohibiciones

- No usar `any`.
- No usar npm ni yarn.
- No crear un segundo lockfile.
- No modificar `schema.prisma` fuera del proceso de migración aprobado.
- No ejecutar migraciones destructivas sin decisión explícita.
- No acceder a Prisma directamente desde controladores.
- No mezclar Taller comercial con Taller ejecutor.
- No tratar al Taller como propietario del vehículo o historial.
- No permitir que Mecánico o Admin rechacen una tarea en nombre de la Flota.
- No autoaprobar órdenes vencidas.
- No autorresolver alertas críticas.
- No usar `timestampLocal` para validar la ventana offline.
- No devolver stack traces en Producción.
- No subir secretos, `.env` ni credenciales al repositorio.
- No agregar roles sin modificar primero la Constitución de forma aprobada.
- No agregar funcionalidades fuera de V1 sin decisión formal de producto.
- No implementar pasarela de pagos, planes personalizados por Flota,
  multi-taller comercial real, app nativa o telemetría como parte de V1.

## 4. Protocolo de desarrollo con SpecKit

- El backlog oficial está en `docs/planning/backlog.md`.
- Cada Feature se selecciona desde el backlog antes de iniciar trabajo.
- La secuencia obligatoria es:
  `Constitution → Specification → Clarification → Planning → Tasks →
  Implementation`.
- No se implementa una Feature sin una Specification aprobada.
- Las ambigüedades de dominio deben resolverse antes del Planning.
- Las Tasks deben referenciar los bounded contexts, entidades, workflows e
  invariantes relevantes de `docs/domain/`.
- Una implementación que contradice esta Constitución debe detenerse y
  solicitar una enmienda formal.

## 5. AI Protocol

- Ningún agente puede implementar código dentro de `src/` sin un Action Plan
  aprobado previamente.
- El Action Plan debe derivarse de una Specification y sus Clarifications.
- Antes de modificar código, el agente debe consultar:
  `files/CONSTITUTION.md`, `files/schema.prisma`, `files/product-analysis.md`
  y los documentos relevantes de `docs/domain/`.
- El agente debe declarar cualquier contradicción entre fuentes y aplicar la
  jerarquía de autoridad definida por el proyecto.
- El agente no debe inventar funcionalidades, entidades, roles o transiciones
  no respaldadas por la documentación aprobada.
- Toda modificación de reglas de negocio debe identificar su impacto en
  lenguaje ubicuo, bounded contexts, entidades, workflows y glosario.

## 6. Governance

- Esta Constitución es obligatoria para Features, cambios de schema, PRs y
  acciones de agentes.
- Las fuentes de negocio de mayor prioridad conservan autoridad sobre las
  decisiones de producto; esta Constitución agrega restricciones técnicas.
- Toda excepción requiere:
  1. motivo explícito;
  2. impacto documentado;
  3. aprobación del responsable del proyecto;
  4. actualización de esta Constitución si la excepción se vuelve permanente.
- Las revisiones deben verificar seguridad, aislamiento tenant, invariantes de
  dominio, cobertura de pruebas y ausencia de secretos.
- Los cambios incompatibles requieren una enmienda versionada antes de
  implementarse.

**Versión**: 1.0.0  
**Ratificada**: 2026-07-20  
**Última modificación**: 2026-07-20
