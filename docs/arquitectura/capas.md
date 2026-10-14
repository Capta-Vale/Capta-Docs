# Capas de la arquitectura

> Detalle de cada capa y sus responsabilidades.

---

## 📚 Las 5 capas

```
1. Presentación  →  Capta.WebApplication
2. Negocio       →  Capta.Core (Managers, DomainObjects)
3. Datos         →  Capta.Core (DAOs)
4. Modelo        →  Capta.Model
5. Contratos     →  Capta.Contracts
```

---

## 1️⃣ Capa de Presentación

**Proyecto:** `Capta.WebApplication`

**Responsabilidad:** manejar peticiones HTTP, renderizar vistas, delegar al negocio.

### Componentes

| Componente | Función |
|---|---|
| **Controllers** | Reciben la petición, orquestan, devuelven JSON/View |
| **Views** (`.cshtml`) | Renderizan HTML |
| **Scripts** (`.js`) | Comportamiento del cliente (Knockout) |
| **Filters** | Validan permisos, sesión, errores |
| **Helpers** | Utilidades de presentación |
| **App_Start** | Configuración de la app |

### Reglas

- **No contener lógica de negocio.** Solo orquestación.
- **Validar permisos** al inicio del método.
- **Devolver JSON** para llamadas AJAX.
- **No llamar a DAOs directamente.** Siempre vía Manager.
- **No instanciar `DBContext`.**

### Ejemplo correcto

```csharp
public class CatalogoController : Common.BaseController
{
    private readonly Managers2.CatalogoManager _manager;

    public CatalogoController(Managers2.CatalogoManager catalogoManager)
    {
        _manager = catalogoManager;
    }

    [Securirty.Imp.CustomAuthorizationFilter(Contracts.Enums.Permisos.Lectura)]
    [Securirty.Imp.CheckAccess(Contracts.Enums.AzmanOperation.Catalogo)]
    public JsonResult ObtenerPaises()
    {
        var paises = _manager.ObtenerPaises();
        return Json(paises, JsonRequestBehavior.AllowGet);
    }
}
```

---

## 2️⃣ Capa de Negocio

**Proyecto:** `Capta.Core` (carpetas `Managers/` y `DomainObjects/`)

**Responsabilidad:** contener la lógica de negocio, orquestar operaciones, validar reglas.

### Componentes

| Componente | Función | Responsabilidad |
|---|---|---|
| **Managers** | Fachada de negocio | Agrupa operaciones de varios DomainObjects |
| **DomainObjects** | Orquestación | Coordina múltiples DAOs, abre transacciones, aplica reglas |

### Reglas

- **Los Managers** son delgados: delegan a DomainObjects.
- **Los DomainObjects** pueden tocar varios DAOs.
- **Las transacciones** se manejan aquí.
- **Las reglas de negocio** se aplican aquí.
- **Nunca devuelven `Entity`**, siempre `Info`.

### Ejemplo correcto — Manager

```csharp
public class CatalogoManager
{
    public List<PaisInfo> ObtenerPaises()
    {
        var dao = new DomainObjects.CatalogoDomainObjects();
        return dao.ObtenerPaises();
    }
}
```

### Ejemplo correcto — DomainObject

```csharp
internal class EvaluacionFinancieraDomainObjects
{
    public void Guardar(EvaluacionFinancieraInfo info, string identityName)
    {
        using (var dbCore = new Model.Context.Core.DBContext())
        {
            var dao = new Integration.DAO.EvaluacionFinancieraDAO(dbCore);
            var enlaceDAO = new Integration.DAO.EnlaceDAO(dbCore);

            try
            {
                var enlace = enlaceDAO.ObtenerPorId(info.EnlaceId);
                if (enlace.Estatus == Estatus.Bloqueado)
                    throw new BusinessException("La cuenta está bloqueada.");

                dao.Guardar(info, identityName);
            }
            catch (Exception ex)
            {
                ex.Save(ImpactLevel.High, ex.Message, identityName);
                throw;
            }
        }
    }
}
```

---

## 3️⃣ Capa de Acceso a Datos

**Proyecto:** `Capta.Core` (carpeta `Integration/DAO/`)

**Responsabilidad:** mapear `Info` ↔ `Entity`, ejecutar queries y SPs.

### Reglas

- **Heredan de `Common.BaseClassDAO`.**
- **Son `internal`, no `public`.**
- **Tienen 2 constructores:** vacío y con `DBContext`.
- **Usan `MapInfo<Info, Entity>`** para mapeo.
- **Devuelven `Info`, nunca `Entity`.**
- **Usan `Constants.*`** para nombres de tablas/SPs.
- **Los SPs se llaman con Dapper.**

### Ejemplo correcto

```csharp
internal class CatalogoDAO : Common.BaseClassDAO
{
    public CatalogoDAO() { }
    public CatalogoDAO(Model.Context.Core.DBContext context) : base(context) { }

    public List<PaisInfo> ObtenerPaises()
    {
        var query = Db.Pais.Where(a => a.Activo);

        var qSelect = from a in query
                      select MapInfo<Model.Context.Core.Entity.Pais, PaisInfo>(a);

        return qSelect.ToListReadUncommitted();
    }
}
```

---

## 4️⃣ Capa de Modelo

**Proyecto:** `Capta.Model`

**Responsabilidad:** definir entidades y contexto de BD.

### Componentes

| Componente | Función |
|---|---|
| **DBContext** | Punto de entrada a la BD |
| **Entity** | Representación de tablas |
| **Constants** | Nombres de tablas, campos, SPs |

### Reglas

- **Cada entidad hereda de `Isi.LRFramework.Entidad`.**
- **El `[TableName]` viene de `Constants.TableName`.**
- **Los nombres de propiedades coinciden con columnas.**
- **No hay lógica en las entidades.**

### Ejemplo correcto

```csharp
[TableName(Constants.TableName.Pais)]
public class Pais : Isi.LRFramework.Entidad
{
    public string Descripcion { get; set; }
    public bool Activo { get; set; }
}
```

---

## 5️⃣ Capa de Contratos

**Proyecto:** `Capta.Contracts`

**Responsabilidad:** definir los DTOs entre capas.

### Componentes

| Componente | Sufijo | Función |
|---|---|---|
| **Info** | `Info` | DTO de datos |
| **Filter** | `Filter` | Parámetros de búsqueda |
| **Enum** | — | Enumeraciones |
| **Constants** | — | Constantes del dominio |

### Reglas

- **Sin lógica.**
- **Sin dependencias de otras capas.**
- **Los `Filter` heredan de `Common.BaseFilter`.**

### Ejemplo correcto

```csharp
public class PaisInfo
{
    public int Id { get; set; }
    public string Descripcion { get; set; }
    public bool Activo { get; set; }
}

public class PaisFilter : Common.BaseFilter
{
    public string Descripcion { get; set; }
    public bool? Activo { get; set; }
}
```

---

## 🔗 Comunicación entre capas

### Reglas

| Desde | Hacia | Permitido |
|---|---|---|
| Controller | Manager | ✅ |
| Manager | DomainObjects | ✅ |
| Manager | DAO | ✅ (cuando no necesita transacción) |
| DomainObjects | DAO | ✅ |
| DAO | DBContext | ✅ |
| DAO | Constants | ✅ |
| Controller | DAO | ❌ |
| Controller | DBContext | ❌ |
| Manager | Entity | ❌ |
| Frontend | DAO | ❌ |

### Diagrama

```
Controller ─→ Manager ─→ DomainObjects ─→ DAO ─→ DB
    │            │
    │            └──→ DAO (sin transacción)
    │
    └──→ (nunca a DAO directo)
```

---

## 🎯 Responsabilidades por capa (resumen)

| Capa | Qué hace | Qué NO hace |
|---|---|---|
| **Controller** | Recibe HTTP, valida permisos, devuelve JSON | Lógica de negocio, queries |
| **Manager** | Fachada, agrupa DomainObjects | Transacciones, queries |
| **DomainObjects** | Orquesta DAOs, transacciones, reglas | HTTP, JSON |
| **DAO** | Acceso a datos, mapeo Info↔Entity | Reglas de negocio, transacciones |
| **Entity** | Representa tabla | Lógica |
| **Info** | Contrato | Lógica |

---

*Última actualización: 2026-10-14*