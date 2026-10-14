# Estándar de nombres

> Convenciones de nomenclatura en Capta.Credivoucher.

---

## 🎯 Principios

1. **Claridad sobre brevedad.** Un nombre descriptivo vale más que uno corto.
2. **Consistencia con el código existente.** Antes de inventar, buscar el patrón.
3. **Inglés para conceptos técnicos, español para dominio.**
4. **Sin abreviaturas ambiguas.** `usr` → `usuario`, `pgo` → `pago`.

---

## 📛 Clases

`PascalCase`. Sustantivo singular.

| Correcto | Incorrecto |
|---|---|
| `Cliente` | `clientes` |
| `ClienteInfo` | `ClienteDTO` |
| `CatalogoManager` | `CatMan` |
| `BitacoraOperacionDAO` | `BitDAO` |

---

## 📛 Interfaces

`PascalCase` con prefijo `I`.

| Correcto | Incorrecto |
|---|---|
| `IRepository` | `RepositoryInterface` |
| `IEmailService` | `EmailServiceI` |

---

## 📛 Métodos

`PascalCase`. Verbo + sustantivo.

| Acción | Español | Inglés |
|---|---|---|
| Crear | `Guardar`, `Insertar` | `Save`, `Create` |
| Actualizar | `Actualizar`, `Guardar` | `Update` |
| Eliminar lógico | `Desactivar` | `Disable` |
| Eliminar físico | `Eliminar` | `Delete` |
| Obtener uno | `ObtenerPorId` | `GetById` |
| Obtener varios | `ObtenerPorFiltro` | `GetByFilter` |
| Obtener paginado | `ObtenerPaginado` | `GetPager` |
| Validar | `Validar` | `Validate` |
| Procesar | `Procesar` | `Process` |
| Enviar | `Enviar` | `Send` |
| Calcular | `Calcular` | `Calculate` |

**Regla:** mantener el estilo del archivo que se modifica.

---

## 📛 Propiedades

`PascalCase`. Sustantivo.

| Correcto | Incorrecto |
|---|---|
| `Nombre` | `nombre` |
| `FechaRegistro` | `Fecha_Registro` |
| `EstaActivo` | `Activo_Flag` |

---

## 📛 Campos privados

`_camelCase`. Prefijo `_`.

```csharp
private readonly CatalogoManager _manager;
private int _contador;
```

---

## 📛 Variables locales

`camelCase`.

```csharp
var cliente = new Cliente();
var totalPagar = 1000m;
```

---

## 📛 Parámetros

`camelCase`.

```csharp
public void Guardar(ClienteInfo cliente, string identityName)
```

---

## 📛 Constantes

`PascalCase`. En clases de constantes.

```csharp
public const string NombreTabla = "Cliente";
```

---

## 📁 Sufijos obligatorios

| Elemento | Sufijo | Ejemplo |
|---|---|---|
| Contrato entre capas | `Info` | `ClienteInfo` |
| Parámetros de búsqueda | `Filter` | `ClienteFilter` |
| DAO | `DAO` | `ClienteDAO` |
| Manager | `Manager` | `ClienteManager` |
| DomainObject | `DomainObjects` | `ClienteDomainObjects` |
| Controller | `Controller` | `ClienteController` |
| Reporte | `Report` o `Reporte` | `CorteReport` |
| Exportación | `Export` | `ClienteExport` |

---

## 🗂️ Nombres de archivos

| Tipo | Convención | Ejemplo |
|---|---|---|
| Clases C# | Igual que la clase | `ClienteController.cs` |
| Vistas MVC | Igual que la acción | `Index.cshtml`, `Create.cshtml` |
| Vistas parciales | Prefijo `_` | `_GridCliente.cshtml` |
| JavaScript | Igual que la vista + `.js` | `Index.js`, `Create.js` |
| Scripts SQL | `[PROYECTO]-Descripcion.sql` | `[CAPTA]-Agregar-Celular-Cliente.sql` |

---

## 🌐 Nombres de tablas

`PascalCase`, singular.

| Correcto | Incorrecto |
|---|---|
| `Cliente` | `clientes` |
| `BitacoraOperacion` | `Bitacora_Operacion` |
| `PermisoUsuarioRol` | `Permiso_Usuario_Rol` |

---

## 🌐 Nombres de columnas

`PascalCase`.

| Correcto | Incorrecto |
|---|---|
| `FechaRegistro` | `Fecha_Registro` |
| `UsuarioModifico` | `usuario_modifico` |

---

## 🌐 Nombres de SPs

`PascalCase` con prefijo del verbo.

| Prefijo | Uso | Ejemplo |
|---|---|---|
| `sp_Get` | Obtener | `sp_GetCorteByFecha` |
| `sp_Insert` | Insertar | `sp_InsertCorte` |
| `sp_Update` | Actualizar | `sp_UpdateCorte` |
| `sp_Delete` | Eliminar | `sp_DeleteCorte` |
| `Gestionar` | Operación compleja | `GestionarProyeccionEnlaces` |
| `Generar` | Generación | `GenerarTapeCartera` |

---

## 🌐 Nombres de constantes en C#

| Clase | Prefijo | Ejemplo |
|---|---|---|
| `TableName` | — | `Constants.TableName.Cliente` |
| `Field` | — | `Constants.Field.Id` |
| `StoredProcedure` | — | `Constants.StoredProcedure.GestionarProyeccion` |
| `Parameter` | — | `Constants.Parameter.Codigo` |
| `Message` | — | `Constants.Message.ClienteBloqueado` |
| `General` | — | `Constants.General.DateDefault` |

---

## 🚫 Antipatrones de nombres

| Antipatrón | Ejemplo | Correcto |
|---|---|---|
| Abreviaturas | `usr`, `pgo` | `usuario`, `pago` |
| Números | `Cliente1`, `Cliente2` | `Cliente`, `ClienteSecundario` |
| Letras solas | `x`, `y`, `z` | `cliente`, `totalPagar` |
| Tipos en nombre | `ClienteObj`, `ClienteStr` | `Cliente` |
| Prefijos húngaros | `strNombre`, `intEdad` | `Nombre`, `Edad` |
| Guiones bajos | `nombre_cliente` | `NombreCliente` |
| Palabras reservadas | `class`, `public` | `Clase`, `Publico` |

---

## ✅ Checklist

Antes de hacer commit, verifica:

- [ ] Clases en `PascalCase`
- [ ] Métodos en `PascalCase`
- [ ] Propiedades en `PascalCase`
- [ ] Campos privados en `_camelCase`
- [ ] Variables locales en `camelCase`
- [ ] Constantes en `PascalCase`
- [ ] Sufijos correctos (`Info`, `Filter`, `DAO`, `Manager`)
- [ ] Sin abreviaturas ambiguas
- [ ] Sin prefijos húngaros
- [ ] Archivos con el mismo nombre que la clase principal

---

*Última actualización: 2026-10-14*