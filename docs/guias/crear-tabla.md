# Guía: Crear una tabla

> Cómo agregar una nueva tabla a la base de datos CaptaQA.

---

## Cuándo usar

- Nueva funcionalidad que requiere persistencia.
- Normalmente acompañada de [crear-endpoint.md](crear-endpoint.md).

---

## Paso 1 — Diseñar la tabla

Antes de crear:

- [ ] Nombre descriptivo en `PascalCase`.
- [ ] PK `Id` identity.
- [ ] Campos con tipos correctos (`DECIMAL(18,2)` para dinero).
- [ ] Campos de auditoría: `FechaRegistro`, `UsuarioRegistro`.
- [ ] FK a tablas relacionadas.
- [ ] Índices en columnas de búsqueda.

---

## Paso 2 — Crear el script SQL

`Scripts/Tables/[CAPTA]-Nombre-Tabla.sql`:

```sql
-- ========================================================
-- Script: [CAPTA]-ClienteDireccion
-- Autor: Tu Nombre
-- Fecha: 2026-10-14
-- Ticket: TIAGA-999
-- ========================================================

IF NOT EXISTS (SELECT 1 FROM sys.tables WHERE name = 'ClienteDireccion')
BEGIN
    CREATE TABLE ClienteDireccion (
        Id INT IDENTITY(1,1) PRIMARY KEY,
        ClienteId INT NOT NULL,
        Calle NVARCHAR(200) NULL,
        Colonia NVARCHAR(200) NULL,
        Ciudad NVARCHAR(100) NULL,
        Estado NVARCHAR(100) NULL,
        CodigoPostal NVARCHAR(10) NULL,
        Activo BIT NOT NULL DEFAULT 1,
        FechaRegistro DATETIME NOT NULL DEFAULT GETDATE(),
        UsuarioRegistro NVARCHAR(256) NULL,
        CONSTRAINT FK_ClienteDireccion_Cliente 
            FOREIGN KEY (ClienteId) REFERENCES Cliente(Id)
    );

    CREATE INDEX IX_ClienteDireccion_ClienteId 
        ON ClienteDireccion(ClienteId);
END
GO
```

---

## Paso 3 — Ejecutar en desarrollo

1. Abre SSMS.
2. Selecciona `CaptaQA`.
3. Ejecuta el script.

---

## Paso 4 — Crear la constante

`Capta.Model/Constants/TableName.cs`:

```csharp
public const string ClienteDireccion = "ClienteDireccion";
```

---

## Paso 5 — Crear la entidad

`Capta.Model/Context/Core/Entity/ClienteDireccion.cs`:

```csharp
[TableName(Constants.TableName.ClienteDireccion)]
public class ClienteDireccion : Isi.LRFramework.Entidad
{
    public int ClienteId { get; set; }
    public string Calle { get; set; }
    public string Colonia { get; set; }
    public string Ciudad { get; set; }
    public string Estado { get; set; }
    public string CodigoPostal { get; set; }
    public bool Activo { get; set; }
    public DateTime FechaRegistro { get; set; }
    public string UsuarioRegistro { get; set; }
}
```

---

## Paso 6 — Registrar en el DBContext

`Capta.Model/Context/Core/DBContext.cs`:

```csharp
public IQueryable<Entity.ClienteDireccion> ClienteDireccion 
    => Tabla<Entity.ClienteDireccion>();
```

---

## Paso 7 — Crear el Info

`Capta.Contracts/Info/ClienteDireccionInfo.cs`:

```csharp
public class ClienteDireccionInfo
{
    public int Id { get; set; }
    public int ClienteId { get; set; }
    public string Calle { get; set; }
    public string Colonia { get; set; }
    public string Ciudad { get; set; }
    public string Estado { get; set; }
    public string CodigoPostal { get; set; }
    public bool Activo { get; set; }
}
```

---

## Paso 8 — Compilar

Verifica que todo compile. Si hay errores, revisar:

- Constante en `TableName`.
- Propiedad en `DBContext`.
- Entidad con `[TableName]` correcto.

---

## Paso 9 — Migrar a QA y Producción

Coordinar con el DBA para ejecutar el script en cada ambiente.

---

## ✅ Checklist

- [ ] Nombre en `PascalCase`
- [ ] PK `Id` identity
- [ ] Campos de auditoría
- [ ] FK a tablas relacionadas
- [ ] Índices en columnas de búsqueda
- [ ] Script versionado
- [ ] Constante en `TableName`
- [ ] Entidad creada
- [ ] Propiedad en `DBContext`
- [ ] Info creado
- [ ] Compila
- [ ] Script ejecutado en QA
- [ ] Script ejecutado en Producción

---

*Última actualización: 2026-10-14*