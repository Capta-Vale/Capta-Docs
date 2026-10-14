# Decisiones arquitectónicas (ADRs)

> Registro de decisiones importantes y sus fundamentos. Antes de proponer un cambio, revisa si ya se decidió.

---

## 📖 Formato

Cada decisión sigue el formato ADR (Architecture Decision Record):

- **Estado:** Aceptada / Reemplazada / Deprecada
- **Contexto:** por qué surgió la decisión
- **Decisión:** qué se decidió
- **Consecuencias:** qué implicaciones tiene

---

## ADR-001 — Usar 3 ORMs

**Estado:** ✅ Aceptada
**Fecha:** 2014 (histórica)

### Contexto

El proyecto empezó con ADO.NET puro. Con el tiempo se agregaron BLToolkit (2015), EF6 (2016) y Dapper (2020) para casos específicos.

### Decisión

Mantener los 3 ORMs:

- **EF6:** para operaciones CRUD estándar.
- **BLToolkit:** para entidades legacy y `MapInfo`.
- **Dapper:** para SPs complejos y queries de alto rendimiento.

### Consecuencias

- ✅ Cada ORM se usa donde es más eficiente.
- ❌ Mayor complejidad para nuevos desarrolladores.
- ⚠️ **Regla:** no introducir un 4to ORM.

---

## ADR-002 — Un solo WebApplication

**Estado:** ✅ Aceptada
**Fecha:** 2014

### Contexto

El sistema tiene múltiples módulos (Canje, Cobranza, Prospectos, etc.).

### Decisión

Todos los módulos viven en `Capta.WebApplication`. No se separan en aplicaciones independientes.

### Consecuencias

- ✅ Un solo despliegue, una sola sesión.
- ✅ Reutilización de código común.
- ❌ Un cambio en un módulo requiere redesplegar todo.
- ⚠️ **Regla:** los módulos se separan por carpetas, no por aplicaciones.

---

## ADR-003 — Azman para permisos

**Estado:** ✅ Aceptada
**Fecha:** 2014

### Contexto

Se necesitaba un sistema de permisos granular por operación, no solo por controlador.

### Decisión

Usar **Azman** con las tablas `PermisoRolControlador` y `PermisoRolOperation`.

### Consecuencias

- ✅ Permisos granulares por operación.
- ✅ Administrable desde BD.
- ❌ Mayor complejidad que `[Authorize(Roles="X")]`.
- ⚠️ **Regla:** todo endpoint nuevo requiere `[CheckAccess(OperationX)]`.

---

## ADR-004 — KnockoutJS para frontend

**Estado:** ✅ Aceptada
**Fecha:** 2016

### Contexto

Se necesitaba reactividad sin un build step complejo.

### Decisión

Usar **KnockoutJS** con `ko.observable` y `ko.mapping.fromJS`.

### Consecuencias

- ✅ Curva de aprendizaje baja.
- ✅ Sin build step.
- ❌ Menos performante que frameworks modernos.
- ⚠️ **Regla:** no migrar a otro framework sin aprobación del Comité.

---

## ADR-005 — Servicios Windows para procesos batch

**Estado:** ✅ Aceptada
**Fecha:** 2019

### Contexto

Algunos procesos son pesados y no pueden correr en el contexto web.

### Decisión

Los procesos batch (bloqueo nocturno, envío STP, SMS, colas, tape) viven en **6 servicios Windows** separados.

### Consecuencias

- ✅ Los procesos batch no afectan al WebApplication.
- ✅ Cada servicio se escala independientemente.
- ❌ Mayor complejidad operativa.
- ⚠️ **Regla:** los procesos que duren más de 5 minutos van a servicio Windows.

---

## ADR-006 — Reportes en background

**Estado:** ✅ Aceptada
**Fecha:** 2020

### Contexto

Los reportes grandes bloqueaban al usuario.

### Decisión

Usar `ReporteSegundoPlanHelper` con `Task.Run` y polling desde el frontend.

### Consecuencias

- ✅ El usuario no espera bloqueado.
- ✅ Los reportes grandes no afectan el rendimiento del servidor.
- ⚠️ **Regla:** reportes que duren más de 5 segundos van en background.

---

## ADR-007 — Contratos en `Capta.Contracts`

**Estado:** ✅ Aceptada
**Fecha:** 2014

### Contexto

Se necesitaba desacoplar las capas.

### Decisión

Todos los DTOs (`Info`, `Filter`, `Enum`, `Constants`) viven en `Capta.Contracts`. No dependen de otras capas.

### Consecuencias

- ✅ Capas desacopladas.
- ✅ Fácil testing.
- ⚠️ **Regla:** `Capta.Contracts` no puede referenciar `Capta.Core` ni `Capta.Model`.

---

## ADR-008 — Un `DBContext` por operación

**Estado:** ✅ Aceptada
**Fecha:** 2016

### Contexto

Compartir `DBContext` entre operaciones causaba problemas de concurrencia y estados inconsistentes.

### Decisión

Cada `DomainObject` abre su propio `DBContext` con `using`.

### Consecuencias

- ✅ Operaciones aisladas.
- ✅ Sin memory leaks.
- ❌ Overhead de abrir conexión.
- ⚠️ **Regla:** `DBContext` se abre en el DomainObject, no en el Controller ni el Manager.

---

## ADR-009 — Sin tests automatizados (legacy)

**Estado:** ⚠️ Deuda técnica
**Fecha:** 2014

### Contexto

El proyecto empezó sin tests. Se agregó una carpeta `Capta.Test` pero no se mantiene.

### Decisión

**Decisión actual:** no hay tests automatizados obligatorios. Se depende de QA manual.

**Decisión futura:** agregar tests gradualmente (ver roadmap).

### Consecuencias

- ❌ Regresiones silenciosas.
- ❌ Miedo a refactorizar.
- ⚠️ **Plan:** ver roadmap 2026 en `docs/procesos/qa.md`.

---

## ADR-010 — Bitácora por tabla específica

**Estado:** ✅ Aceptada
**Fecha:** 2015

### Contexto

Se necesitaba auditar diferentes operaciones con estructuras distintas.

### Decisión

Cada operación sensible tiene su **propia tabla de bitácora**, pero **todas siguen el mismo patrón** (usuario, fecha, valor anterior, valor nuevo, motivo).

### Consecuencias

- ✅ Estructuras óptimas por operación.
- ✅ Consultas rápidas.
- ❌ Más tablas que mantener.
- ⚠️ **Regla:** toda operación sensible debe tener su bitácora con el patrón común.

---

## ADR-011 — Sin ORM nuevo

**Estado:** ✅ Aceptada
**Fecha:** 2023

### Contexto

Se propuso migrar todo a Entity Framework Core.

### Decisión

**No** migrar. Mantener los 3 ORMs actuales hasta que se decida migrar todo el stack a .NET 8+.

### Consecuencias

- ✅ Evitar una migración masiva riesgosa.
- ✅ Estabilidad del sistema.
- ⚠️ **Plan:** evaluar migración completa en roadmap 2026-2027.

---

## ADR-012 — Sin Docker/Kubernetes (actualmente)

**Estado:** ⚠️ En evaluación
**Fecha:** 2026

### Contexto

Se propuso containerizar el sistema.

### Decisión

**No** containerizar el sistema actual (.NET Framework 4.8 no soporta Linux nativo).

### Consecuencias

- ✅ Sin complicaciones de infra.
- ❌ No hay escalabilidad horizontal fácil.
- ⚠️ **Plan:** migrar a .NET 8 + Docker en roadmap 2026-2027.

---

## 📋 Reglas para agregar una ADR

1. **Identificar** la decisión.
2. **Documentar** contexto, decisión y consecuencias.
3. **Validar** con el Comité de Arquitectura.
4. **Asignar** número correlativo (`ADR-NNN`).
5. **Actualizar** este archivo.

---

*Última actualización: 2026-10-14*
*Responsable: Comité de Arquitectura*