# Patrones del sistema

> Patrones de diseño recurrentes en Capta.Credivoucher. Aplícalos para mantener consistencia.

---

## 📚 Índice de patrones

| # | Patrón | Uso |
|---|---|---|
| 1 | DAO con Info/Entity | Acceso a datos |
| 2 | Manager-DomainObject | Fachada de negocio |
| 3 | Filter + Paginación | Búsquedas |
| 4 | Transacción explícita | Operaciones atómicas |
| 5 | Upsert con `Db.Save` | Insertar o actualizar |
| 6 | Stored Procedure con Dapper | SPs complejos |
| 7 | Bitácora de operaciones | Auditoría |
| 8 | Autorización por rol | Validación de permisos |
| 9 | Notificación por tipo | Correos |
| 10 | Reporte en background | Reportes grandes |
| 11 | Configuración en BD | Catálogos dinámicos |
| 12 | Cache en memoria | Catálogos estáticos |

---

## Patrón 1 — DAO con Info/Entity

**Cuándo usar:** siempre que accedas a la BD.

**Regla:** los DAOs devuelven `Info`, nunca `Entity`.

```csharp
internal class CatalogoDAO : Common.BaseClassDAO
{
    public CatalogoDAO() { }
    public CatalogoDAO(Model.Context.Core.DBContext context) : base(context) { }

    public List<PaisInfo> ObtenerPaises()
    {
        var query = Db.Pais.Where(a => a.Activo);

        var qSelect = from a in query
                      select MapInfo<Entity.Pais, PaisInfo>(a);

        return qSelect.ToListReadUncommitted();
    }
}
```

**Ventajas:**

- Desacopla la capa de datos de la de negocio.
- Facilita el testing.
- Evita exponer entidades con navegación.

---

## Patrón 2 — Manager-DomainObject

**Cuándo usar:** cuando necesitas agrupar operaciones.

**Regla:** el Manager es delgado, el DomainObject hace el trabajo.

```csharp
// Manager
public class CatalogoManager
{
    public List<PaisInfo> ObtenerPaises()
    {
        var dao = new DomainObjects.CatalogoDomainObjects();
        return dao.ObtenerPaises();
    }
}

// DomainObjects
internal class CatalogoDomainObjects
{
    public List<PaisInfo> ObtenerPaises()
    {
        using (var dbCore = new Model.Context.Core.DBContext())
        {
            using (var dao = new Integration.DAO.CatalogoDAO(dbCore))
            {
                return dao.ObtenerPaises();
            }
        }
    }
}
```

---

## Patrón 3 — Filter + Paginación

**Cuándo usar:** búsquedas con múltiples criterios y paginación.

```csharp
// Filter
public class ClienteFilter : Common.BaseFilter
{
    public string Descripcion { get; set; }
    public int SucursalId { get; set; }
    public Estatus? Estatus { get; set; }
}

// DAO
public Pager<ClienteInfo> ObtenerPaginado(ClienteFilter filter)
{
    var query = Db.Cliente.AsQueryable();

    if (filter.SucursalId > 0)
        query = query.Where(c => c.SucursalId == filter.SucursalId);

    if (filter.Estatus.HasValue)
        query = query.Where(c => c.Estatus == filter.Estatus.Value);

    var total = query.Count();
    var items = query.OrderBy(c => c.Nombre)
                     .Skip(filter.Page * filter.Rows)
                     .Take(filter.Rows)
                     .Select(c => MapInfo<Entity.Cliente, ClienteInfo>(c))
                     .ToList();

    return new Pager<ClienteInfo>
    {
        Items = items,
        TotalCount = total,
        CurrentPage = filter.Page,
        PageSize = filter.Rows
    };
}
```

**`BaseFilter` incluye:**

- `Page`
- `Rows`
- `SortBy`
- `IsAsc`
- `IdentityName`

---

## Patrón 4 — Transacción explícita

**Cuándo usar:** cuando afectas múltiples tablas y necesitas atomicidad.

```csharp
using (var connection = GetConnection())
{
    connection.Open();
    using (var transaction = connection.BeginTransaction())
    {
        try
        {
            connection.Execute(sp1, parameters1, transaction: transaction);
            connection.Execute(sp2, parameters2, transaction: transaction);

            transaction.Commit();
            return 1;
        }
        catch (System.Exception)
        {
            transaction.Rollback();
            throw;
        }
    }
}
```

**Reglas:**

- `Commit` y `Rollback` explícitos.
- `throw;` (no `throw ex;`).
- Loggear la excepción antes de re-lanzar.

---

## Patrón 5 — Upsert con `Db.Save`

**Cuándo usar:** cuando no sabes si es insert o update.

```csharp
var entity = MapInfo<Info.Elemento, Entity.Elemento>(info);
Db.Save(entity);   // insert si Id == 0, update si Id != 0
return entity.Id;
```

**Regla:** `Db.Save` de BLToolkit decide automáticamente.

---

## Patrón 6 — Stored Procedure con Dapper

**Cuándo usar:** SPs de SQL Server con parámetros.

```csharp
var parameters = new DynamicParameters();
parameters.Add(Constants.Parameter.Codigo, item.Codigo);
parameters.Add(Constants.Parameter.Sucursal, item.Sucursal);
parameters.Add(Constants.Parameter.Proyeccion, item.Proyeccion);

connection.Execute(
    Constants.StoredProcedure.GestionarProyeccionEnlaces,
    parameters,
    transaction: transaction,
    commandType: CommandType.StoredProcedure
);
```

**Reglas:**

- Nombres de SPs en `Constants.StoredProcedure`.
- Nombres de parámetros en `Constants.Parameter`.
- Siempre con `transaction` cuando hay más de una operación.

---

## Patrón 7 — Bitácora de operaciones

**Cuándo usar:** toda operación que modifique el patrimonio o datos críticos.

```csharp
// Entity
[TableName(Constants.TableName.BitacoraOperacion)]
public class BitacoraOperacion : Isi.LRFramework.Entidad
{
    public int ElementoId { get; set; }
    public string ValorAnterior { get; set; }
    public string ValorNuevo { get; set; }
    public string UsuarioEjecutivo { get; set; }
    public string UsuarioAutorizador { get; set; }
    public string TipoAutorizador { get; set; }
    public string Comentario { get; set; }
    public DateTime FechaOperacion { get; set; }
}

// DAO
internal class BitacoraOperacionDAO : Common.BaseClassDAO
{
    public int Insertar(BitacoraOperacionInfo info)
    {
        info.FechaOperacion = Db.ServerDate();

        var entity = MapInfo<BitacoraOperacionInfo, Entity.BitacoraOperacion>(info);
        Db.Save(entity);
        return entity.Id;
    }
}
```

**Reglas:**

- Bitácora inmutable (sin `UPDATE`/`DELETE`).
- Guarda valor anterior y nuevo.
- Guarda usuario, fecha y motivo.

---

## Patrón 8 — Autorización por rol

**Cuándo usar:** cuando una operación requiere autorización de un rol específico.

```csharp
public JsonResult ValidarAutorizacion(string password, string tipoAutorizador)
{
    var identityName = User.Identity.Name;

    var adminManager = new Managers2.AdministracionManager();
    var rolesUsuario = adminManager.ObtenerRolesPorUsuario(identityName);
    var esSupervisor = rolesUsuario.Any(r =>
        string.Equals(r.Descripcion, "SUPERVISOR", StringComparison.OrdinalIgnoreCase));

    if (!esSupervisor && !EsAutorizadorValido(identityName, tipoAutorizador))
    {
        return Json(new { Error = 1, Mensaje = "No autorizado." });
    }

    if (!ValidarPassword(password, identityName))
    {
        return Json(new { Error = 1, Mensaje = "Contraseña incorrecta." });
    }

    return Json(new
    {
        Error = 0,
        TipoAutorizadorReal = esSupervisor ? "supervisor" : tipoAutorizador
    });
}
```

**Reglas:**

- Validar primero el rol, después la contraseña.
- Un rol superior puede autorizar rangos inferiores.
- Devolver el rol real, no el solicitado.

---

## Patrón 9 — Notificación por tipo

**Cuándo usar:** notificaciones por correo configurables.

```csharp
try
{
    var destinatarios = configDAO.ObtenerPorTipo(TipoNotificacion.X);
    if (destinatarios.Any())
    {
        var enviado = EmailHelper.SendEmail(destinatarios, asunto, cuerpo, out string error);
        if (!enviado)
            Logger.Info($"[EMAIL] Fallo: {error}");
    }
}
catch (Exception ex)
{
    Logger.Info($"[EMAIL] Excepción: {ex.Message}");
}
```

**Reglas:**

- Destinatarios configurables, no hardcodeados.
- Envío dentro de `try/catch` (no rompe el flujo).
- Loguear errores.

---

## Patrón 10 — Reporte en background

**Cuándo usar:** reportes que tardan más de 5 segundos.

```csharp
// Controller
public JsonResult IniciarReporte(ReportFilter filter)
{
    var key = ReporteSegundoPlanHelper.Start(User.Identity.Name, () =>
        Task.Run(() => _manager.GenerarReporte(filter)));

    return Json(new { Key = key, Mensaje = "Procesando..." });
}

// Polling
public JsonResult ConsultarEstatus(string key)
{
    var estatus = ReporteSegundoPlanHelper.GetEstatus(key, User.Identity.Name);
    return Json(estatus);
}
```

**Reglas:**

- Generar GUID único por reporte.
- Estado en caché con TTL.
- Polling cada 2 segundos.

---

## Patrón 11 — Configuración en BD

**Cuándo usar:** valores que cambian sin desplegar.

```csharp
[TableName(Constants.TableName.ConfiguracionNotificacion)]
public class ConfiguracionNotificacion : Isi.LRFramework.Entidad
{
    public int TipoNotificacion { get; set; }
    public string Email { get; set; }
    public bool Activo { get; set; }
}
```

**Reglas:**

- Nada de `appSettings` para configuraciones de negocio.
- Todo en tablas.
- Administrable por Operación.

---

## Patrón 12 — Cache en memoria

**Cuándo usar:** catálogos que no cambian seguido.

```csharp
private static List<PaisInfo> _cachePaises;
private static DateTime _cacheExpira = DateTime.MinValue;

public List<PaisInfo> ObtenerPaises()
{
    if (_cachePaises == null || DateTime.Now > _cacheExpira)
    {
        _cachePaises = CargarPaises();
        _cacheExpira = DateTime.Now.AddMinutes(30);
    }
    return _cachePaises;
}
```

**Reglas:**

- Solo para catálogos estáticos.
- TTL explícito.
- Invalidación manual disponible.

---

*Última actualización: 2026-10-14*