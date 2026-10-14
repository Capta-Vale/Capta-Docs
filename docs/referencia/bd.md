# Bases de datos

> Las 4 bases de datos del sistema Capta.Credivoucher.

---

## 🗄️ CaptaQA

**Propósito:** core del negocio.

**Tamaño:** ~350 tablas.

**Contenido:**

- Clientes, Enlaces
- Vales, Pagarés
- Pagos, Abonos
- Convenios, Condonaciones
- Prospectos
- Fideicomiso
- Seguridad

---

## 📊 DatawareHouseDB

**Propósito:** analítica y reportes.

**Tamaño:** ~12 tablas.

**Contenido:**

- TapeCartera (V1, V2)
- CarteraCorte
- CreditosActivos
- CreditosQuebrantados

---

## 💵 SavingDB

**Propósito:** ahorro e inversiones.

**Tamaño:** ~6 tablas.

**Contenido:**

- Investment
- Partner
- Withdrawal
- Payment

---

## 🤖 DatosAnalizadosDB

**Propósito:** OCR e IA.

**Tamaño:** ~4 tablas.

**Contenido:**

- AnalisisDocumento
- AnalisisEstadoCuenta
- ProyeccionSugerida

---

## 🔄 Flujo entre BDs

```
CaptaQA (transaccional)
    ▼ (ETL nocturno)
DatawareHouseDB (analítica)
    ▼
Reportes ejecutivos

CaptaQA
    ▼ (Job ahorro)
SavingDB

CaptaQA
    ▼ (OCR + IA)
DatosAnalizadosDB
```

---

## 🔌 Conexiones

Cada proyecto tiene sus connection strings en `Web.config`/`App.config`.

| Connection string | BD |
|---|---|
| `ModelCoreConnection` | CaptaQA |
| `ModelCoreConnectionDWH` | DatawareHouseDB |
| `ModelCoreConnectionSavings` | SavingDB |
| `DatosAnalizadosConnection` | DatosAnalizadosDB |

---

*Última actualización: 2026-10-14*