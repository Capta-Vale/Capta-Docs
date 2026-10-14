# Bienvenida

> Todo lo que necesitas saber en tu primer día.

---

## ¿Qué es Capta.Credivoucher?

**Capta.Credivoucher** es el sistema core de Capta Vale. Administra el ciclo completo de:

- **Vales y pagarés** — Desde la asignación hasta la liquidación.
- **Cobranza** — Captura de abonos, cortes y convenios.
- **Prospectos** — Afiliación de nuevos distribuidores.
- **Riesgo** — Buró de Crédito y análisis IA.
- **Fideicomiso** — Cesiones de cartera.
- **Cierre** — Tape de cartera mensual.

**Usuarios del sistema:**

| Rol | Uso |
|---|---|
| Gestor | Canje, cobranza, atención a clientes |
| Coordinador | Supervisión de sucursal |
| Afiliación | Alta de nuevos prospectos |
| Mesa de Control | Autorización de operaciones especiales |
| Auditoría | Consulta y reportes |
| Administrador | Configuración del sistema |

---

## 🎯 ¿Qué vas a hacer?

### Si eres desarrollador backend

- Trabajarás con ASP.NET MVC 5, C# y .NET Framework 4.8.
- Escribirás Controllers, Managers, DomainObjects y DAOs.
- Consultarás SQL Server (CaptaQA, DWH, Savings, DatosAnalizados).

### Si eres desarrollador frontend

- Trabajarás con KnockoutJS, Bootstrap 3, jQuery y Kendo UI.
- Modificarás vistas `.cshtml` y archivos `Index.js`.
- Aplicarás el design system.

### Si eres QA

- Validarás flujos operativos completos.
- Verificarás reglas de negocio.
- Reportarás bugs con evidencia.

---

## 🧭 Cómo navegar este manual

### Ruta 1 — Onboarding (nuevo en el equipo)

1. Esta página (bienvenida)
2. [Setup local](setup-local.md)
3. [Tu primer cambio](primer-cambio.md)
4. [Onboarding completo](../procesos/onboarding.md)

### Ruta 2 — Hacer un cambio

1. [Estándares de código](../estandares/nombres.md)
2. [Guía específica](../guias/crear-endpoint.md)
3. [Reglas de negocio](../reglas/negocio.md)
4. [Proceso de code review](../procesos/code-review.md)

### Ruta 3 — Entender el sistema

1. [Arquitectura](../arquitectura/vision-general.md)
2. [Capas](../arquitectura/capas.md)
3. [Patrones](../arquitectura/patrones.md)
4. [Decisiones](../arquitectura/decisiones.md)
5. [Antipatrones](../arquitectura/antipatrones.md)

---

## 📖 Glosario rápido

| Término | Significado |
|---|---|
| **Enlace** | Distribuidor de vales |
| **Vale** | Documento prepago entregado al cliente |
| **Pagaré** | Documento de deuda derivado de un vale canjeado |
| **Canje** | Proceso de convertir un vale en pagaré + transferencia |
| **Abono** | Pago parcial de un pagaré |
| **Corte** | Cierre quincenal de cobranza |
| **Convenio** | Reestructuración de deuda |
| **Condonación** | Cancelación parcial o total de deuda |
| **Tape** | Archivo mensual con el detalle de la cartera |
| **Prospecto** | Candidato a convertirse en enlace |
| **Buró** | Buró de Crédito |
| **CeroPapel** | Servicio de firma digital |

📖 Glosario completo: [docs/apendices/glosario.md](../apendices/glosario.md)

---

## 🛠️ Herramientas que vas a usar

| Herramienta | Para qué |
|---|---|
| Visual Studio 2022 | IDE principal |
| SQL Server Management Studio | Consultas SQL |
| Git + GitHub | Control de versiones |
| Tiaga | Sistema de tickets |
| Postman | Probar APIs |
| Chrome DevTools | Debug frontend |

---

## ✅ Antes de empezar

Antes de tocar código, asegúrate de:

- [ ] Tienes acceso al repositorio de GitHub.
- [ ] Tienes acceso a Tiaga.
- [ ] Tienes Visual Studio 2022 instalado.
- [ ] Tienes SQL Server Management Studio.
- [ ] Tienes acceso a las bases de datos de desarrollo.
- [ ] Leíste los [Estándares de código](../estandares/codigo-cs.md).

---

## 🚀 Siguiente paso

→ [Setup local](setup-local.md)