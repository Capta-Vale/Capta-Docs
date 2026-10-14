# Estándar de código C#

> Reglas de escritura de código C# en Capta.Credivoucher.

---

## 🎯 Principios

1. **Legibilidad antes que cleverness.**
2. **Consistencia con el código existente.**
3. **Documentar decisiones no obvias.**
4. **No dejar código muerto.**

---

## 📏 Formato

### Indentación

- 4 espacios.
- No tabs.

### Llaves

```csharp
// Correcto
if (condicion)
{
    // ...
}

// Incorrecto
if (condicion) {
    // ...
}
```

### Líneas en blanco

- 1 línea entre métodos.
- 1 línea entre bloques lógicos.
- No más de 2 líneas consecutivas.

### Longitud de línea

- Máximo 120 caracteres.
- Si excede, dividir en varias líneas.

---

## 🔴 Las 15 reglas obligatorias

### C#-01 — No `using` sin usar

```csharp
// Correcto
using System;
using System.Collections.Generic;

// Incorrecto
using System;
using System.Collections.Generic;
using System.Text;   // ← no se usa
```

### C#-02 — No variables sin usar

```csharp
// Correcto
var cliente = ObtenerCliente(id);
Guardar(cliente);

// Incorrecto
var cliente = ObtenerCliente(id);
var sinUsar = "esto no se usa";   // ← eliminar
Guardar(cliente);
```

### C#-03 — Métodos con menos de 100 líneas

Si un método excede 100 líneas, dividirlo:

```csharp
public void Procesar()
{
    Validar();
    Calcular();
    Persistir();
    Notificar();
}

private void Validar() { /* ... */ }
private void Calcular() { /* ... */ }
private void Persistir() { /* ... */ }
private void Notificar() { /* ... */ }
```

### C#-04 — Archivos con menos de 2000 líneas

Si un archivo excede 2000 líneas, dividir en `partial class`.

### C#-05 — Comentario XML en métodos públicos

```csharp
/// <summary>
/// Obtiene la lista filtrada de clientes.
/// </summary>
/// <param name="filter">Filtros de búsqueda.</param>
/// <returns>Lista de clientes.</returns>
public List<ClienteInfo> ObtenerPorFiltro(ClienteFilter filter)
{
    // ...
}
```

### C#-06 — No `catch (Exception) { }` vacío

```csharp
// Correcto
try { /* ... */ }
catch (Exception ex)
{
    ex.Save(Contracts.Enums.ImpactLevel.High, ex.Message, identityName);
    throw;
}

// Incorrecto
try { /* ... */ }
catch (Exception) { }   // ← traga el error
```

### C#-07 — No `throw ex;`

```csharp
// Correcto
catch (Exception ex)
{
    ex.Save(...);
    throw;
}

// Incorrecto
catch (Exception ex)
{
    throw ex;   // ← pierde stack trace
}
```

### C#-08 — No strings mágicos

```csharp
// Correcto
parameters.Add(Constants.Parameter.Codigo, item.Codigo);
connection.Execute(Constants.StoredProcedure.GestionarProyeccion, parameters);

// Incorrecto
parameters.Add("@Codigo", item.Codigo);
connection.Execute("GestionarProyeccion", parameters);
```

### C#-09 — No `Thread.Sleep` en código nuevo

```csharp
// Correcto
await Task.Delay(5000);

// Incorrecto
Thread.Sleep(5000);   // ← bloquea el thread
```

### C#-10 — No sync-over-async

```csharp
// Correcto
var result = await SomeAsyncMethod();

// Incorrecto
var result = SomeAsyncMethod().Result;   // ← puede causar deadlock
var result = SomeAsyncMethod().Wait();   // ← puede causar deadlock
```

### C#-11 — DAOs devuelven `Info`, nunca `Entity`

```csharp
// Correcto
public List<ClienteInfo> ObtenerPorFiltro(ClienteFilter filter)
{
    var query = Db.Cliente.AsQueryable();
    var qSelect = from c in query
                  select MapInfo<Entity.Cliente, ClienteInfo>(c);
    return qSelect.ToListReadUncommitted();
}

// Incorrecto
public List<Cliente> ObtenerPorFiltro(ClienteFilter filter)
{
    return Db.Cliente.ToList();   // ← devuelve Entity
}
```

### C#-12 — Transacciones con `Commit` y `Rollback`

```csharp
using (var connection = GetConnection())
{
    connection.Open();
    using (var transaction = connection.BeginTransaction())
    {
        try
        {
            // ...
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

### C#-13 — Correos dentro de `try/catch`

```csharp
try
{
    var enviado = EmailHelper.SendEmail(destinatarios, asunto, cuerpo, out string error);
    if (!enviado)
        Logger.Info($"[EMAIL] Fallo: {error}");
}
catch (Exception ex)
{
    Logger.Info($"[EMAIL] Excepción: {ex.Message}");
}
```

### C#-14 — Nombres de tablas/SPs en `Constants.*`

```csharp
[TableName(Constants.TableName.Cliente)]
public class Cliente : Isi.LRFramework.Entidad { /* ... */ }
```

### C#-15 — `Info` y `Filter` con esos sufijos

```csharp
public class ClienteInfo { /* ... */ }
public class ClienteFilter : Common.BaseFilter { /* ... */ }
```

---

## 🎨 Estilo del código

### Var implícito

Usar `var` cuando el tipo sea obvio:

```csharp
var cliente = new Cliente();               // ✅ obvio
var lista = ObtenerClientes();              // ✅ obvio
List<ClienteInfo> clientes = new List<ClienteInfo>();   // ✅ explícito si no es obvio
var x = 5;                                  // ✅ obvio
```

### Inicialización de objetos

```csharp
// Correcto
var cliente = new Cliente
{
    Nombre = "Juan",
    Apellido = "Pérez"
};

// Incorrecto
var cliente = new Cliente();
cliente.Nombre = "Juan";
cliente.Apellido = "Pérez";
```

### Interpolación de strings

```csharp
// Correcto
var mensaje = $"Hola {nombre}, tienes {edad} años.";

// Incorrecto
var mensaje = "Hola " + nombre + ", tienes " + edad + " años.";
```

### LINQ

```csharp
// Correcto
var activos = clientes.Where(c => c.Activo).ToList();

// Incorrecto
var activos = new List<Cliente>();
foreach (var c in clientes)
    if (c.Activo)
        activos.Add(c);
```

### Null checking

```csharp
// Correcto
if (cliente == null) return;

// Correcto (C# 6+)
var nombre = cliente?.Nombre ?? "Sin nombre";

// Incorrecto
if (cliente != null && cliente.Nombre != null) { /* ... */ }
```

---

## 📝 Comentarios

### Cuándo comentar

- El **por qué** de una decisión no obvia.
- Referencias a reglas de negocio (`// Regla RN-A01`).
- Workarounds temporales (`// TODO: remover cuando...`).

### Cuándo NO comentar

- El **qué** (el código ya lo dice).
- Código obvio.
- Código muerto (eliminar en lugar de comentar).

### Ejemplo correcto

```csharp
// Se valida la contraseña una sola vez para evitar múltiples llamadas al UserManager
if (!ValidarPassword(password, identityName))
{
    return Json(new { Error = 1, Mensaje = "Contraseña no válida." });
}
```

### Ejemplo incorrecto

```csharp
// Valida la contraseña
if (!ValidarPassword(password, identityName))   // ← obvio
{
    return Json(new { Error = 1, Mensaje = "Contraseña no válida." });
}
```

---

## ✅ Checklist

- [ ] No `using` sin usar
- [ ] No variables sin usar
- [ ] Métodos <100 líneas
- [ ] Archivos <2000 líneas
- [ ] Métodos públicos con XML
- [ ] No `catch (Exception) { }` vacío
- [ ] No `throw ex;`
- [ ] No strings mágicos
- [ ] No `Thread.Sleep` nuevo
- [ ] No sync-over-async
- [ ] DAOs devuelven `Info`
- [ ] Transacciones con `Commit`/`Rollback`
- [ ] Correos en `try/catch`
- [ ] Constants en `Constants.*`
- [ ] Sufijos `Info`/`Filter` correctos
- [ ] Comentarios explican el "por qué"

---

*Última actualización: 2026-10-14*