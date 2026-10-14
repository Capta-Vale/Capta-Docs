# Guía: Crear Web API

> Cómo crear una nueva Web API en Capta.Credivoucher.

---

## Cuándo usar

- Exponer funcionalidad a servicios externos.
- Consumir desde apps móviles.
- Integración con terceros.

---

## Estructura

```
Capta.Nombre.Api/
├── Controllers/
├── App_Start/
├── Web.config
└── Global.asax
```

---

## Paso 1 — Crear el proyecto

1. **File → New → Project**.
2. **ASP.NET Web Application (.NET Framework)**.
3. Framework: **.NET Framework 4.8**.
4. Template: **Web API**.

---

## Paso 2 — Agregar referencias

Agregar referencias a:

- `Capta.Core`
- `Capta.Contracts`
- `Capta.Model`

---

## Paso 3 — Configurar autenticación

En `App_Start/WebApiConfig.cs`:

```csharp
public static class WebApiConfig
{
    public static void Register(HttpConfiguration config)
    {
        // Rutas
        config.MapHttpAttributeRoutes();
        config.Routes.MapHttpRoute(
            name: "DefaultApi",
            routeTemplate: "api/{controller}/{id}",
            defaults: new { id = RouteParameter.Optional }
        );

        // Formato JSON por defecto
        config.Formatters.JsonFormatter.SerializerSettings.ContractResolver =
            new CamelCasePropertyNamesContractResolver();

        // Filtros globales
        config.Filters.Add(new AuthorizeAttribute());
    }
}
```

---

## Paso 4 — Crear el controller

```csharp
[RoutePrefix("api/cliente")]
public class ClienteController : ApiController
{
    private readonly NombreManager _manager = new NombreManager();

    [HttpGet]
    [Route("{id:int}")]
    public IHttpActionResult ObtenerPorId(int id)
    {
        try
        {
            var cliente = _manager.ObtenerPorId(id);

            if (cliente == null)
                return NotFound();

            return Ok(cliente);
        }
        catch (Exception ex)
        {
            Logger.Error(ex);
            return InternalServerError(ex);
        }
    }

    [HttpPost]
    [Route("")]
    public IHttpActionResult Guardar(ClienteInfo info)
    {
        try
        {
            var manager = new ClienteManager();
            manager.Guardar(info, "API");
            return Ok(new { Exito = true });
        }
        catch (Exception ex)
        {
            Logger.Error(ex);
            return InternalServerError(ex);
        }
    }
}
```

---

## Paso 5 — Configurar seguridad

En `Web.config`:

```xml
<appSettings>
    <add key="ApiKey" value="..." />
</appSettings>
```

En el controller:

```csharp
[ApiKeyAuthorize]
public class ClienteController : ApiController
{
    // ...
}
```

O con token Bearer.

---

## Paso 6 — Probar con Postman

**GET:** `https://localhost:44301/api/cliente/123`

**POST:** `https://localhost:44301/api/cliente`

Body:

```json
{
    "nombre": "Juan",
    "apellido": "Pérez"
}
```

---

## Reglas de oro

1. **Autenticación obligatoria.**
2. **Try/catch** en todos los endpoints.
3. **Logging** de errores.
4. **No exponer entidades** (solo `Info`).
5. **Versionado** de la API (`/api/v1/...`).

---

## ✅ Checklist

- [ ] Proyecto creado (.NET Framework 4.8)
- [ ] Referencias agregadas
- [ ] `WebApiConfig` configurado
- [ ] Autenticación configurada
- [ ] Controllers creados
- [ ] Rutas definidas
- [ ] Logging configurado
- [ ] Probado con Postman
- [ ] Documentado en Swagger/OpenAPI

---

*Última actualización: 2026-10-14*