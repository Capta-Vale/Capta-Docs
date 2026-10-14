# Estándar de código SQL

> Reglas de escritura de SQL en Capta.Credivoucher.

---

## 🎯 Principios

1. Legibilidad.
2. Performance.
3. Consistencia con el esquema existente.
4. Nombres descriptivos.

---

## 📏 Formato

- Palabras reservadas en MAYÚSCULAS.
- Identificadores en PascalCase.
- Indentación de 4 espacios.
- Punto y coma al final.

---

## 🔴 Reglas obligatorias

### SQL-01 — Nombres de tablas en `PascalCase`

```sql
CREATE TABLE Cliente ( ... );
```

### SQL-02 — Nombres de columnas en `PascalCase`

```sql
FechaRegistro DATETIME NOT NULL,
UsuarioModifico NVARCHAR(256) NULL
```

### SQL-03 — PK `Id` autoincremental

```sql
CREATE TABLE Cliente (
    Id INT IDENTITY(1,1) PRIMARY KEY,
    ...
);
```

### SQL-04 — Sin `SELECT *`

```sql
-- Correcto
SELECT Id, Nombre, Apellido FROM Cliente;

-- Incorrecto
SELECT * FROM Cliente;
```

### SQL-05 — Sin concatenación de strings

```sql
-- Correcto (parametrizado)
DECLARE @Id INT = 5;
SELECT * FROM Cliente WHERE Id = @Id;

-- Incorrecto
SELECT * FROM Cliente WHERE Id = ' + @Id + ';
```

### SQL-06 — Índices en columnas de búsqueda

```sql
CREATE INDEX IX_Cliente_SucursalId ON Cliente(SucursalId);
CREATE INDEX IX_Cliente_Estatus ON Cliente(Estatus);
```

### SQL-07 — Stored Procedures con prefijo

```sql
CREATE PROCEDURE sp_GetClienteById
    @Id INT
AS
BEGIN
    SELECT Id, Nombre FROM Cliente WHERE Id = @Id;
END
```

### SQL-08 — Transacciones con `TRY/CATCH`

```sql
BEGIN TRY
    BEGIN TRANSACTION;
    -- operaciones
    COMMIT TRANSACTION;
END TRY
BEGIN CATCH
    IF @@TRANCOUNT > 0
        ROLLBACK TRANSACTION;
    THROW;
END CATCH
```

### SQL-09 — `READ UNCOMMITTED` para consultas de solo lectura

```sql
SELECT Id, Nombre 
FROM Cliente WITH (NOLOCK)
WHERE Estatus = 1;
```

### SQL-10 — Fechas con `GETDATE()` o `SYSDATETIME()`

```sql
FechaRegistro DATETIME NOT NULL DEFAULT GETDATE()
```

---

## 🎨 Estructura de un script

```sql
-- ========================================================
-- Script: [CAPTA]-Agregar-Celular-Cliente
-- Autor: Tu Nombre
-- Fecha: 2026-10-14
-- Ticket: TIAGA-999
-- ========================================================

IF NOT EXISTS (
    SELECT 1 FROM sys.columns 
    WHERE object_id = OBJECT_ID('Cliente') 
    AND name = 'Celular'
)
BEGIN
    ALTER TABLE Cliente
    ADD Celular NVARCHAR(20) NULL;
END
GO
```

---

## 🚫 Antipatrones

| Antipatrón | Correcto |
|---|---|
| `SELECT *` | Seleccionar columnas explícitas |
| Concatenar SQL | Parametrizar |
| Sin índices | Agregar índices a columnas de búsqueda |
| `DROP TABLE` directo | Verificar dependencias primero |
| Modificar datos sin `WHERE` | Siempre con `WHERE` |
| Sin transacción para múltiples operaciones | Usar `BEGIN TRAN` |

---

## ✅ Checklist

- [ ] Nombres en `PascalCase`
- [ ] PK `Id` identity
- [ ] Sin `SELECT *`
- [ ] Sin concatenación
- [ ] Índices en columnas de búsqueda
- [ ] Transacciones con `TRY/CATCH`
- [ ] `NOLOCK` para consultas de lectura
- [ ] Script versionado en `Scripts/Tables/`
- [ ] Comentario de cabecera con autor, fecha, ticket

---

*Última actualización: 2026-10-14*