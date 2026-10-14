# Antipatrones

> Lo que **NO** debe hacerse en Capta.Credivoucher. Cada antipatrón incluye su alternativa correcta.

---

## 🚫 Antipatrón 1 — Lógica de negocio en Controller

**Incorrecto:**

```csharp
public JsonResult Guardar(EvaluacionFinancieraInfo info)
{
    if (info.LimiteCredito > info.MontoPagare)
        throw new Exception("El límite no puede exceder el pagaré.");

    if (info.Estatus == Estatus.Baja && info.Proyeccion > 0)
        throw new Exception("No se puede dar de baja con proyección activa.");

    var dao = new EvaluacionFinancieraDAO();
    dao.Guardar(info);

    return Json(new { Exito = true });
}
```

**Correcto:**

```csharp
// Controller
public JsonResult Guardar(EvaluacionFinancieraInfo info)
{
    var manager = new EvaluacionFinancieraManager();
    manager.Guardar(info, User.Identity.Name);
    return Json(new { Exito = true });
}

// Manager
public void Guardar(EvaluacionFinancieraInfo info, string identityName)
{
    var dom = new DomainObjects.EvaluacionFinancieraDomainObjects();
    dom.Guardar(info, identityName);
}

// DomainObject (aquí van las reglas)
public void Guardar(EvaluacionFinancieraInfo info, string identityName)
{
    if (info.LimiteCredito > info.MontoPagare)
        throw new BusinessException("El límite no puede exceder el pagaré.");
    // ... etc
}
```

---

## 🚫 Antipatrón 2 — SQL embebido en código

**Incorrecto:**

```csharp
var query = "SELECT * FROM Cliente WHERE Id = " + id;
Db.Execute(query);
```

**Correcto:**

```csharp
var query = Db.Cliente.Where(c => c.Id == id);
var result = query.ToListReadUncommitted();
```

**Regla:** nunca concatenar SQL. Siempre LINQ o parámetros.

---

## 🚫 Antipatrón 3 — Llamadas HTTP desde JS a servicios externos

**Incorrecto:**

```javascript
fetch('https://api.stp.mx/transferencias', {
    method: 'POST',
    body: JSON.stringify(data)
});
```

**Correcto:**

```javascript
fetch('/Transferencia/Guardar', {
    method: 'POST',
    body: JSON.stringify(data)
});
```

**Regla:** todo servicio externo se llama desde el backend, no desde el frontend.

---

## 🚫 Antipatrón 4 — Sesión para estado de negocio

**Incorrecto:**

```csharp
Session["carrito"] = listaCarrito;
Session["usuarioTemporal"] = usuario;
```

**Correcto:**

```csharp
var carrito = new CarritoInfo { ... };
dao.Guardar(carrito);
```

**Regla:** la sesión solo se usa para autenticación y datos temporales de UI.

---

## 🚫 Antipatrón 5 — Strings mágicos

**Incorrecto:**

```csharp
var query = "SELECT * FROM Cliente WHERE SucursalId = @id";
connection.Execute("sp_GestionarProyeccion", parameters);
```

**Correcto:**

```csharp
var query = Db.Cliente.Where(c => c.SucursalId == id);
connection.Execute(Constants.StoredProcedure.GestionarProyeccion, parameters);
```

**Regla:** nombres de tablas/SPs en `Constants.*`.

---

## 🚫 Antipatrón 6 — Traga-excepciones

**Incorrecto:**

```csharp
try { /* ... */ }
catch (Exception) { }
```

**Correcto:**

```csharp
try { /* ... */ }
catch (Exception ex)
{
    ex.Save(ImpactLevel.High, ex.Message, identityName);
    throw;
}
```

**Regla:** toda excepción se loggea y se re-lanza (o se maneja explícitamente).

---

## 🚫 Antipatrón 7 — `throw ex;`

**Incorrecto:**

```csharp
catch (Exception ex) { throw ex; }
```

**Correcto:**

```csharp
catch (Exception ex)
{
    ex.Save(ImpactLevel.High, ex.Message, identityName);
    throw;
}
```

---

## 🚫 Antipatrón 8 — DAO que devuelve Entity

**Incorrecto:**

```csharp
public Cliente ObtenerPorId(int id)
{
    return Db.Cliente.FirstOrDefault(c => c.Id == id);
}
```

**Correcto:**

```csharp
public ClienteInfo ObtenerPorId(int id)
{
    var query = Db.Cliente.Where(c => c.Id == id);
    var qSelect = from c in query
                  select MapInfo<Entity.Cliente, ClienteInfo>(c);
    return qSelect.FirstOrDefault();
}
```

**Regla:** los DAOs nunca exponen entidades.

---

## 🚫 Antipatrón 9 — Compartir DBContext

**Incorrecto:**

```csharp
private static DBContext _contextoCompartido = new DBContext();

public void Operacion1() { _contextoCompartido.Save(x); }
public void Operacion2() { _contextoCompartido.Save(y); }
```

**Correcto:**

```csharp
public void Operacion1()
{
    using (var db = new DBContext()) { /* ... */ }
}

public void Operacion2()
{
    using (var db = new DBContext()) { /* ... */ }
}
```

**Regla:** un `DBContext` por operación.

---

## 🚫 Antipatrón 10 — `Task.Wait()` / `.Result`

**Incorrecto:**

```csharp
var task = SomeAsyncMethod();
var result = task.Result;
```

**Correcto:**

```csharp
var result = await SomeAsyncMethod();
```

**Regla:** no sync-over-async en código nuevo.

---

## 🚫 Antipatrón 11 — Métodos gigantes

**Incorrecto:**

```csharp
public void Procesar()
{
    // 500 líneas de código
}
```

**Correcto:**

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

**Regla:** máximo 100 líneas por método.

---

## 🚫 Antipatrón 12 — Acoplamiento a la UI

**Incorrecto:**

```csharp
public string Generar()
{
    return "<div>Hola</div>";
}
```

**Correcto:**

```csharp
public JsonResult Obtener()
{
    return Json(new { Mensaje = "Hola" });
}
```

**Regla:** el backend devuelve datos, no presentación.

---

## 🚫 Antipatrón 13 — Hardcodear configuración

**Incorrecto:**

```csharp
var emailDestinatario = "gerente@captasoluciones.com";
```

**Correcto:**

```csharp
var destinatarios = configDAO.ObtenerPorTipo(TipoNotificacion.X);
```

**Regla:** todo lo configurable va en BD o `Constants`.

---

## 🚫 Antipatrón 14 — Clases con múltiples responsabilidades

**Incorrecto:**

```csharp
public class ClienteManager
{
    public void GuardarCliente() { /* ... */ }
    public void EnviarCorreo() { /* ... */ }
    public void GenerarReporte() { /* ... */ }
    public void ExportarExcel() { /* ... */ }
}
```

**Correcto:**

```csharp
public class ClienteManager
{
    public void GuardarCliente() { /* ... */ }
}

public class NotificacionManager
{
    public void EnviarCorreo() { /* ... */ }
}

public class ReporteManager
{
    public void GenerarReporte() { /* ... */ }
}
```

**Regla:** una clase, una responsabilidad.

---

## 🚫 Antipatrón 15 — Nombres vagos

**Incorrecto:**

```csharp
public void Procesar(object data) { /* ... */ }
var x = Calcular(y);
var tmp = new List<Cliente>();
```

**Correcto:**

```csharp
public void ProcesarAutorizacion(AutorizacionInfo autorizacion) { /* ... */ }
var totalPagar = CalcularTotalPagar(cliente);
var clientesActivos = new List<Cliente>();
```

**Regla:** nombres descriptivos, no genéricos.

---

## 🚫 Antipatrón 16 — Comentarios obvios

**Incorrecto:**

```csharp
// Incrementa el contador
contador++;

// Retorna verdadero si es activo
return cliente.Activo;
```

**Correcto:**

```csharp
contador++;

// El contador se reinicia cada quincena
// Ver regla de negocio RN-B01
if (contador >= 15)
    contador = 0;
```

**Regla:** solo comentar el **por qué**, no el **qué**.

---

## 🚫 Antipatrón 17 — Ignorar warnings del compilador

**Incorrecto:**

```csharp
#pragma warning disable CS0168
// código con warnings
#pragma warning restore CS0168
```

**Correcto:**

```csharp
// Arreglar el warning
```

**Regla:** los warnings se arreglan, no se ignoran.

---

## ✅ Checklist de antipatrones

Antes de hacer commit, verifica que **NO** tengas:

- [ ] Lógica de negocio en Controller
- [ ] SQL embebido
- [ ] Llamadas HTTP desde JS a servicios externos
- [ ] Sesión para estado de negocio
- [ ] Strings mágicos
- [ ] `catch (Exception) { }` vacío
- [ ] `throw ex;`
- [ ] DAO que devuelve Entity
- [ ] DBContext compartido
- [ ] `Task.Wait()` / `.Result`
- [ ] Métodos >100 líneas
- [ ] Backend que devuelve HTML
- [ ] Configuración hardcodeada
- [ ] Clases con múltiples responsabilidades
- [ ] Nombres vagos
- [ ] Comentarios obvios
- [ ] Warnings ignorados

---

*Última actualización: 2026-10-14*