# Visión general de la arquitectura

> Cómo está construido Capta.Credivoucher por dentro.

---

## 🏛️ Principios arquitectónicos

1. **Separación de responsabilidades** — cada capa tiene una función clara.
2. **Contratos explícitos** — las capas se comunican por DTOs (`Info`, `Filter`).
3. **Simplicidad** — código simple y explícito antes que abstracciones complejas.
4. **Consistencia** — los patrones se repiten en todos los módulos.
5. **Auditabilidad** — las operaciones sensibles dejan rastro.

---

## 🗺️ Vista de capas

```
┌───────────────────────────────────────────────────┐
│  Capta.WebApplication                             │
│  Controllers · Views · Scripts · Filters          │
└────────────────────┬──────────────────────────────┘
                     ▼
┌───────────────────────────────────────────────────┐
│  Capta.Core                                       │
│  Managers · DomainObjects · DAOs · Helpers        │
└────────────────────┬──────────────────────────────┘
                     ▼
┌───────────────────────────────────────────────────┐
│  Capta.Model                                      │
│  DBContext · Entity · Constants                   │
└────────────────────┬──────────────────────────────┘
                     ▼
┌───────────────────────────────────────────────────┐
│  Capta.Contracts                                  │
│  Info · Filter · Enums · Constants                │
└────────────────────┬──────────────────────────────┘
                     ▼
┌───────────────────────────────────────────────────┐
│  SQL Server                                       │
│  CaptaQA · DWH · Savings · DatosAnalizados        │
└───────────────────────────────────────────────────┘
```

---

## 🔄 Flujo de una petición

```
Usuario
  ▼
Navegador (HTTP)
  ▼
IIS / IIS Express
  ▼
Filtros MVC (Autorización, Session, JSON)
  ▼
Controller
  ▼
Manager
  ▼
DomainObject
  ▼
DAO
  ▼
SQL Server
  ▼
[vuelve por la misma cadena]
  ▼
JSON al navegador
  ▼
Render con Knockout
  ▼
Usuario ve el resultado
```

---

## 📦 Proyectos de la solución

| Proyecto | Tipo | Responsabilidad |
|---|---|---|
| `Capta.WebApplication` | Web MVC | Presentación, controllers, vistas |
| `Capta.Core` | Librería | Lógica de negocio, DAOs, Helpers |
| `Capta.Model` | Librería | Entidades y contexto de BD |
| `Capta.Contracts` | Librería | Contratos (DTOs, Enums, Filters) |
| `Capta.Test` | Librería | Tests (parcial) |
| `Capta.Service.*` | Windows Services | 6 servicios autónomos |
| `Capta.*.Api` | Web APIs | 3 APIs externas |

---

## 🗄️ Bases de datos

| BD | Propósito | Tamaño aprox. |
|---|---|---|
| **CaptaQA** | Core del negocio | ~350 tablas |
| **DatawareHouseDB** | Analítica | ~12 tablas |
| **SavingDB** | Ahorro e inversiones | ~6 tablas |
| **DatosAnalizadosDB** | OCR e IA | ~4 tablas |

**Regla:** toda operación transaccional va a CaptaQA.

---

## 🔌 Integraciones externas

| Servicio | Propósito | Tipo |
|---|---|---|
| **CeroPapel** | Firma digital | API REST |
| **Análisis de Riesgo IA** | Scoring | API REST |
| **STP/SPEI** | Transferencias | API REST |
| **Nubarium** | Validación CURP | API REST |
| **Quash** | Scoring crediticio | API REST |
| **SMTP** | Correos | SMTP |
| **SMS** | Mensajería | API REST |
| **OXXO** | Puntos de pago | API REST |

---

## 🎨 Frontend

| Tecnología | Uso |
|---|---|
| **KnockoutJS** | MVVM (bindings) |
| **Bootstrap 3** | Layout y componentes |
| **jQuery 2.1** | Manipulación DOM |
| **Kendo UI** | Grids, combos, datepickers |
| **Select2** | Combos con búsqueda |
| **Toastr** | Notificaciones |
| **SweetAlert2** | Modales de confirmación |

**Regla:** el frontend **no contiene lógica de negocio**.

---

## 🔐 Seguridad

| Componente | Responsabilidad |
|---|---|
| **ASP.NET Identity** | Autenticación |
| **OWIN Cookie** | Manejo de sesión |
| **Azman** | Autorización por rol + operación |
| **Filtros MVC** | Validación de permisos por petición |

**Regla:** ningún endpoint público sin `[Authorize]`.

---

## 📊 Reportes

| Tecnología | Uso |
|---|---|
| **RDLC** | Reportes con diseño |
| **iTextSharp** | Generación PDF |
| **NPOI / ClosedXML / EPPlus** | Excel |
| **CsvHelper** | CSV |

---

## 🚀 Servicios Windows

| Servicio | Función | Frecuencia |
|---|---|---|
| `FileWatcher` | Vigila archivos entrantes | Continuo |
| `CustomerLock` | Bloqueo nocturno de cuentas | Diario 02:00 AM |
| `STPEnvio` | Envío a STP | Continuo |
| `SMS` | Envío de SMS | Continuo |
| `ColaConsulta` | Procesamiento de colas | Continuo |
| `TapeCartera` | Cierre mensual de tape | Mensual |

---

## 🌐 Web APIs

| API | Propósito |
|---|---|
| `Capta.Buro.Api` | Consulta de Buró de Crédito |
| `Capta.VoucherVerify.Api` | Verificación de vales |
| `Capta.FideicomisoCesion.Api` | Cesiones de cartera |

---

## 📈 Escalabilidad

**Estado actual:**

- 2 servidores IIS detrás de un load balancer.
- 1 servidor SQL principal.
- 1 NAS para backups.

**Cuellos de botella conocidos:**

- Consultas SQL complejas en reportes.
- Cálculo de mora en tiempo real.
- Generación masiva de PDFs.

**Mitigaciones:**

- Caché en memoria para catálogos.
- Reportes en background.
- Índices SQL optimizados.

---

## 🎯 Decisiones arquitectónicas clave

| Decisión | Razón |
|---|---|
| **3 ORMs** (EF6, BLToolkit, Dapper) | Historia del proyecto + optimización |
| **Un solo WebApplication** | Simplicidad operativa |
| **Servicios Windows separados** | Desacoplamiento de procesos batch |
| **Azman para permisos** | Granularidad por operación |
| **KnockoutJS** | Reactividad sin build step |

📖 Detalle: [decisiones.md](decisiones.md)

---

## 🚫 Antipatrones

Lo que **NO** debe hacerse:

- ❌ Lógica de negocio en Controllers.
- ❌ SQL embebido en código.
- ❌ Llamadas HTTP desde JS a servicios externos.
- ❌ Sesión para estado de negocio.
- ❌ Strings mágicos.

📖 Detalle: [antipatrones.md](antipatrones.md)

---

*Última actualización: 2026-10-14*
*Responsable: Comité de Arquitectura*