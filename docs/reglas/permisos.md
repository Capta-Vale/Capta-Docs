# Modelo de permisos

> Cómo funciona la seguridad en Capta.Credivoucher.

---

## 🎯 Componentes

| Componente | Responsabilidad |
|---|---|
| **ASP.NET Identity** | Autenticación (login) |
| **OWIN Cookie** | Sesión |
| **Azman** | Autorización (permisos) |
| **Filtros MVC** | Validación por petición |

---

## 🔐 Autenticación

1. Usuario ingresa usuario/contraseña.
2. ASP.NET Identity valida contra `AspNetUsers`.
3. Se crea cookie OWIN.
4. Se guarda `UserLoggedIn` en sesión.

---

## 🔑 Autorización (Azman)

### Tablas

| Tabla | Propósito |
|---|---|
| `Rol` | Roles del sistema |
| `PermisoUsuarioRol` | Roles asignados a usuarios |
| `PermisoRolControlador` | Permisos por controlador |
| `PermisoRolOperation` | Permisos por operación |
| `Operation` | Catálogo de operaciones |
| `PermisoUsuarioSucursal` | Sucursales asignadas a usuarios |

### Flujo

```
Usuario
    ▼
[Authorize] → valida autenticación
    ▼
[ControladorConPermisos] → valida rol
    ▼
[CustomAuthorizationFilter] → valida permiso específico
    ▼
[CheckSession] → valida sesión
    ▼
[CheckAccess(OperationX)] → valida operación
    ▼
Ejecuta el action
```

---

## 📛 Atributos de seguridad

### `[Authorize]`

Requiere autenticación.

### `[CustomAuthorizationFilter(true|false)]`

Valida que el usuario tenga permiso general sobre el controlador.

### `[CheckAccess(OperationX)]`

Valida permiso específico.

### `[Permisos(Lectura|Escritura)]`

Valida tipo de permiso.

---

## 📋 Ejemplo completo

```csharp
[Authorize, Securirty.Imp.CustomAuthorizationFilter(true)]
public class ClienteController : Common.BaseController
{
    [Securirty.Imp.CustomAuthorizationFilter(Contracts.Enums.Permisos.Lectura)]
    [Securirty.Imp.CheckAccess(Contracts.Enums.AzmanOperation.Cliente)]
    public JsonResult ObtenerPorFiltro(ClienteFilter filter)
    {
        // ...
    }
}
```

---

## 🆕 Agregar una nueva operación

1. **Definir la operación** en `AzmanOperation`:

   ```csharp
   public const string NuevaOperacion = "NuevaOperacion";
   ```

2. **Insertar en BD**:

   ```sql
   INSERT INTO Operation (Codigo, Descripcion)
   VALUES ('NuevaOperacion', 'Descripción');
   ```

3. **Asignar a roles**:

   ```sql
   INSERT INTO PermisoRolOperation (RolId, OperationId, Activo)
   SELECT r.Id, o.Id, 1
   FROM Rol r, Operation o
   WHERE r.Descripcion = 'ADMINISTRADOR'
     AND o.Codigo = 'NuevaOperacion';
   ```

4. **Usar el atributo** en el controller:

   ```csharp
   [CheckAccess(AzmanOperation.NuevaOperacion)]
   ```

---

## 🚫 Reglas

1. **Todo endpoint** requiere `[Authorize]`.
2. **Toda operación** requiere `[CheckAccess]`.
3. **Nunca validar permisos en el JS**.
4. **Roles en BD**, no hardcodeados.

---

*Última actualización: 2026-10-14*