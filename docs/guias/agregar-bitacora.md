# Guía: Agregar bitácora

> Cómo agregar una bitácora de operaciones en Capta.Credivoucher.

---

## Cuándo usar

- Operación que modifica el patrimonio.
- Cambios de estatus.
- Cambios de datos críticos.
- Autorizaciones.

---

## Regla institucional

> **RN-E01 — Toda operación sensible deja rastro en bitácora.**

---

## Patrón común

Todas las bitácoras siguen el mismo patrón:

| Campo | Descripción |
|---|---|
| `Id` | PK |
| `{Entidad}Id` | FK a la entidad afectada |
| `ValorAnterior` | Valor previo (JSON o string) |
| `ValorNuevo` | Valor nuevo (JSON o string) |
| `UsuarioEjecutivo` | Quién ejecutó |
| `UsuarioAutorizador` | Quién autorizó (si aplica) |
| `TipoAutorizador` | Rol del autorizador |
| `Comentario` | Motivo |
| `FechaOperacion` | Cuándo |

---

## Paso 1 — Crear la tabla

```sql
CREATE TABLE Bitacora{Entidad} (
    Id INT IDENTITY(1,1) PRIMARY KEY,
    {Entidad}Id INT NOT NULL,
    ValorAnterior NVARCHAR(MAX) NULL,
    ValorNuevo NVARCHAR(MAX) NULL,
    UsuarioEjecutivo NVARCHAR(256) NOT NULL,
    UsuarioAutorizador NVARCHAR(256) NULL,
    TipoAutorizador NVARCHAR(50) NULL,
    Comentario NVARCHAR(500) NULL,
    FechaOperacion DATETIME NOT NULL DEFAULT GETDATE()
);

CREATE INDEX IX_Bitacora{Entidad}_{Entidad}Id 
    ON Bitacora{Entidad}({Entidad}Id);

CREATE INDEX IX_Bitacora{Entidad}_Fecha 
    ON Bitacora{Entidad}(FechaOperacion DESC);
```

---

## Paso 2 — Constante

`Capta.Model/Constants/TableName.cs`:

```csharp
public const string Bitacora{Entidad} = "Bitacora{Entidad}";
```

---

## Paso 3 — Entidad

`Capta.Model/Context/Core/Entity/Bitacora{Entidad}.cs`:

```csharp
[TableName(Constants.TableName.Bitacora{Entidad})]
public class Bitacora{Entidad} : Isi.LRFramework.Entidad
{
    public int {Entidad}Id { get; set; }
    public string ValorAnterior { get; set; }
    public string ValorNuevo { get; set; }
    public string UsuarioEjecutivo { get; set; }
    public string UsuarioAutorizador { get; set; }
    public string TipoAutorizador { get; set; }
    public string Comentario { get; set; }
    public DateTime FechaOperacion { get; set; }
}
```

---

## Paso 4 — Info

`Capta.Contracts/Info/Bitacora{Entidad}Info.cs`:

```csharp
public class Bitacora{Entidad}Info
{
    public int Id { get; set; }
    public int {Entidad}Id { get; set; }
    public string ValorAnterior { get; set; }
    public string ValorNuevo { get; set; }
    public string UsuarioEjecutivo { get; set; }
    public string UsuarioAutorizador { get; set; }
    public string TipoAutorizador { get; set; }
    public string Comentario { get; set; }
    public DateTime FechaOperacion { get; set; }
}
```

---

## Paso 5 — DAO

`Capta.Core/Integration/DAO/Bitacora{Entidad}DAO.cs`:

```csharp
internal class Bitacora{Entidad}DAO : Common.BaseClassDAO
{
    public Bitacora{Entidad}DAO() { }
    public Bitacora{Entidad}DAO(Model.Context.Core.DBContext context) : base(context) { }

    public int Insertar(Bitacora{Entidad}Info info)
    {
        info.FechaOperacion = Db.ServerDate();

        var entity = MapInfo<Bitacora{Entidad}Info, Entity.Bitacora{Entidad}>(info);
        Db.Save(entity);
        return entity.Id;
    }

    public List<Bitacora{Entidad}Info> ObtenerPorEntidad(int entidadId)
    {
        var query = Db.Bitacora{Entidad}
            .Where(x => x.{Entidad}Id == entidadId)
            .OrderByDescending(x => x.Id);

        var qSelect = from a in query
                      select MapInfo<Entity.Bitacora{Entidad}, Bitacora{Entidad}Info>(a);

        return qSelect.ToListReadUncommitted();
    }
}
```

---

## Paso 6 — Registrar en `DBContext`

```csharp
public IQueryable<Entity.Bitacora{Entidad}> Bitacora{Entidad} 
    => Tabla<Entity.Bitacora{Entidad}>();
```

---

## Paso 7 — Usar en el DomainObject

```csharp
public void GuardarOperacion(EntidadInfo info, string identityName)
{
    using (var dbCore = new Model.Context.Core.DBContext())
    {
        var dao = new Integration.DAO.EntidadDAO(dbCore);
        var bitacoraDAO = new Integration.DAO.Bitacora{Entidad}DAO(dbCore);

        // 1. Obtener valor anterior
        var anterior = dao.ObtenerPorId(info.Id);

        // 2. Guardar el cambio
        dao.Guardar(info, identityName);

        // 3. Registrar en bitácora
        var bitacora = new Bitacora{Entidad}Info
        {
            {Entidad}Id = info.Id,
            ValorAnterior = anterior?.Valor?.ToString(),
            ValorNuevo = info.Valor?.ToString(),
            UsuarioEjecutivo = identityName,
            Comentario = info.Comentario
        };

        bitacoraDAO.Insertar(bitacora);
    }
}
```

---

## Reglas de oro

1. **Bitácora inmutable** (sin `UPDATE`/`DELETE`).
2. **Guardar valor anterior y nuevo.**
3. **Usuario, fecha, motivo obligatorios.**
4. **Patrón común** entre todas las bitácoras.

---

## ✅ Checklist

- [ ] Tabla creada con patrón común
- [ ] Constante en `TableName`
- [ ] Entidad creada
- [ ] Info creado
- [ ] DAO creado
- [ ] Registro en `DBContext`
- [ ] Inserción en DomainObject
- [ ] Índices en `{Entidad}Id` y `FechaOperacion`

---

*Última actualización: 2026-10-14*