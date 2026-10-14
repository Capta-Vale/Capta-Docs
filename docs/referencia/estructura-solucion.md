# Estructura de la solución

> Mapa de proyectos, carpetas y su propósito.

---

## 🏗️ Proyectos

```
Capta.Credivoucher.sln
├── Capta.WebApplication         ← Presentación (MVC)
├── Capta.Core                   ← Negocio
├── Capta.Model                  ← Modelo (BLToolkit + EF)
├── Capta.Contracts              ← Contratos
├── Capta.Test                   ← Tests
├── Capta.Service.*              ← Windows Services (6)
├── Capta.*.Api                  ← Web APIs (3)
└── SharedBinaries/              ← Binarios externos
```

---

## 📁 Capta.WebApplication

```
Capta.WebApplication/
├── App_Start/                   ← Configuración
├── Content/                     ← CSS, imágenes
├── Controllers/                 ← ~180 controllers
├── Filters/                     ← Filtros MVC
├── Helpers/                     ← Helpers locales
├── Models/                      ← ViewModels
├── Scripts/                     ← JavaScript
│   ├── app/                     ← Scripts de la app
│   └── lib/                     ← Librerías
├── Securirty/                   ← Azman
├── Views/                       ← ~700 vistas
├── Web.config
└── Global.asax
```

---

## 📁 Capta.Core

```
Capta.Core/
├── Common/                      ← Clases base
├── Helper/                      ← Helpers
├── Integration/DAO/             ← ~150 DAOs
├── Logger/                      ← Logging
├── Servicios/Impl/
│   ├── DomainObjects/           ← ~150
│   ├── Managers/                ← ~140
│   └── UseCases/                ← 2
└── Utilerias/
```

---

## 📁 Capta.Model

```
Capta.Model/
├── Constants/                   ← TableName, Field, etc.
└── Context/
    ├── Core/
    │   ├── DBContext.cs
    │   └── Entity/              ← ~350 entidades
    ├── DatawareHouse/
    ├── Savings/
    └── DatosAnalizados/
```

---

## 📁 Capta.Contracts

```
Capta.Contracts/
├── Constants/
├── Enums/
├── Export/
├── Filter/
├── Info/
└── Interfaces/
```

---

## 🔄 Reglas de organización

### R-ORG-01 — Un archivo por clase
### R-ORG-02 — Un controller por módulo
### R-ORG-03 — Carpetas por capa, no por módulo
### R-ORG-04 — Nombres consistentes
### R-ORG-05 — Sin carpetas nuevas sin aprobación

---

*Última actualización: 2026-10-14*