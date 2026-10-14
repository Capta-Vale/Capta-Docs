# Guía: Crear un endpoint

> Cómo agregar un nuevo endpoint (Controller + Manager + DomainObject + DAO) en Capta.Credivoucher.

---

## Cuándo usar

- Nueva funcionalidad que requiere acceso a datos.
- Nuevo módulo o pantalla.
- Extensión de funcionalidad existente.

---

## Checklist previo

- [ ] Ticket asignado en Tiaga.
- [ ] Requerimiento entendido.
- [ ] Tabla(s) identificadas.
- [ ] Permiso definido.

---

## Paso 1 — Definir el contrato

**Info** (`Capta.Contracts/Info/ClienteInfo.cs`):

```csharp
public class ClienteInfo
{
    public int Id { get; set; }
    public string Nombre { get; set; }
    public string Apellido { get; set; }
    public bool Activo { get; set; }
}
```

**Filter** (`Capta.Contracts/Filter/ClienteFilter.cs`):

```csharp
public class ClienteFilter : Common.BaseFilter
{
    public string Descripcion { get; set; }
    public int SucursalId { get; set; }
}
```

---

## Paso 2 — Verificar la entidad

`Capta.Model/Context/Core/Entity/Cliente.cs`:

```csharp
[TableName(Constants.TableName.Cliente)]
public class Cliente : Isi.LRFramework.Entidad
{
    public string Nombre { get; set; }
    public string Apellido { get; set; }
    public bool Activo { get; set; }
}
```

**Si la tabla no existe**, ver [crear-tabla.md](crear-tabla.md).

---

## Paso 3 — Crear el DAO

`Capta.Core/Integration/DAO/ClienteDAO.cs`:

```csharp
internal class ClienteDAO : Common.BaseClassDAO
{
    public ClienteDAO() { }
    public ClienteDAO(Model.Context.Core.DBContext context) : base(context) { }

    public List<ClienteInfo> ObtenerPorFiltro(ClienteFilter filter)
    {
        var query = Db.Cliente.AsQueryable();

        if (!string.IsNullOrEmpty(filter.Descripcion))
            query = query.Where(c => c.Nombre.Contains(filter.Descripcion));

        if (filter.SucursalId > 0)
            query = query.Where(c => c.SucursalId == filter.SucursalId);

        var qSelect = from c in query
                      select MapInfo<Entity.Cliente, ClienteInfo>(c);

        return qSelect.ToListReadUncommitted();
    }
}
```

---

## Paso 4 — Crear el DomainObject

`Capta.Core/Servicios/Impl/DomainObjects/ClienteDomainObjects.cs`:

```csharp
internal class ClienteDomainObjects
{
    public List<ClienteInfo> ObtenerPorFiltro(ClienteFilter filter)
    {
        using (var dbCore = new Model.Context.Core.DBContext())
        {
            using (var dao = new Integration.DAO.ClienteDAO(dbCore))
            {
                return dao.ObtenerPorFiltro(filter);
            }
        }
    }
}
```

---

## Paso 5 — Crear el Manager

`Capta.Core/Servicios/Impl/Managers/ClienteManager.cs`:

```csharp
public class ClienteManager
{
    public List<ClienteInfo> ObtenerPorFiltro(ClienteFilter filter)
    {
        var dao = new DomainObjects.ClienteDomainObjects();
        return dao.ObtenerPorFiltro(filter);
    }
}
```

---

## Paso 6 — Crear el Controller

`Capta.WebApplication/Controllers/ClienteController.cs`:

```csharp
[Authorize, Securirty.Imp.CustomAuthorizationFilter(true)]
public class ClienteController : Common.BaseController
{
    private readonly Managers2.ClienteManager _manager;

    public ClienteController(Managers2.ClienteManager clienteManager)
    {
        _manager = clienteManager;
    }

    [Securirty.Imp.CustomAuthorizationFilter(Contracts.Enums.Permisos.Lectura)]
    [Securirty.Imp.CheckAccess(Contracts.Enums.AzmanOperation.Cliente)]
    public JsonResult ObtenerPorFiltro(ClienteFilter filter)
    {
        var lista = _manager.ObtenerPorFiltro(filter);
        return Json(new { lista, Error = 0 });
    }
}
```

---

## Paso 7 — Crear la vista (opcional)

Si el endpoint es para una pantalla nueva:

`Capta.WebApplication/Views/Cliente/Index.cshtml`:

```html
@{
    ViewBag.Title = "Clientes";
}

<div class="container-fluid">
    <h1 class="page-title">@ViewBag.Title</h1>
    <!-- contenido -->
</div>

@section Scripts {
    <script src="~/Scripts/Cliente/Index.js"></script>
}
```

---

## Paso 8 — Probar

1. Compila.
2. Ejecuta.
3. Prueba el endpoint con Postman o desde la UI.
4. Verifica en la BD.

---

## Paso 9 — Commit

```powershell
git add .
git commit -m "feat(999-Capta): agregar endpoint Cliente.ObtenerPorFiltro

Refs: TIAGA-999"
```

---

## ✅ Checklist

- [ ] Info creado
- [ ] Filter creado
- [ ] Entidad verificada
- [ ] DAO creado
- [ ] DomainObject creado
- [ ] Manager creado
- [ ] Controller creado
- [ ] Permisos configurados en Azman
- [ ] Probado localmente
- [ ] Commit con mensaje correcto

---

*Última actualización: 2026-10-14*