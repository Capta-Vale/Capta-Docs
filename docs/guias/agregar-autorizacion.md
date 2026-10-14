# Guía: Agregar autorización

> Cómo agregar un flujo de autorización por rol en Capta.Credivoucher.

---

## Cuándo usar

- Una operación requiere autorización de un rol superior.
- El rol autorizador es configurable.
- La autorización debe quedar en bitácora.

---

## Arquitectura

```
Usuario hace operación
    ▼
Sistema valida si requiere autorización
    ▼
Si requiere → abre modal
    ▼
Usuario ingresa contraseña + comentario
    ▼
Backend valida rol + contraseña
    ▼
Si autoriza → guarda en bitácora
    ▼
Continúa el flujo
```

---

## Paso 1 — Definir el rol autorizador

**Regla institucional:** los roles se asignan en la tabla `Rol` + `PermisoUsuarioRol`.

Ejemplo:

```sql
INSERT INTO Rol (Descripcion) VALUES ('AUTORIZADOR X');

INSERT INTO PermisoUsuarioRol (Usuario, RolId)
SELECT 'usuario@captasoluciones.com', Id FROM Rol WHERE Descripcion = 'AUTORIZADOR X';
```

---

## Paso 2 — Backend: `ValidarAutorizacion`

```csharp
[HttpPost]
public JsonResult ValidarAutorizacion(string password, string tipoAutorizador)
{
    var identityName = User.Identity.Name;

    if (string.IsNullOrWhiteSpace(password))
        return Json(new { Error = 1, Mensaje = "Favor de teclear la contraseña." });

    // 1. Validar rol
    var adminManager = new Managers2.AdministracionManager();
    var rolesUsuario = adminManager.ObtenerRolesPorUsuario(identityName);
    var esSupervisor = rolesUsuario.Any(r =>
        string.Equals(r.Descripcion, "SUPERVISOR", StringComparison.OrdinalIgnoreCase));

    var esAutorizadorValido = rolesUsuario.Any(r =>
        string.Equals(r.Descripcion, tipoAutorizador, StringComparison.OrdinalIgnoreCase));

    if (!esSupervisor && !esAutorizadorValido)
        return Json(new { Error = 1, Mensaje = "El usuario no está autorizado." });

    // 2. Validar contraseña
    if (!ValidarPassword(password, identityName))
        return Json(new { Error = 1, Mensaje = "Contraseña incorrecta." });

    // 3. Devolver rol real
    return Json(new
    {
        Error = 0,
        Mensaje = "Autorización válida.",
        TipoAutorizadorReal = esSupervisor ? "supervisor" : tipoAutorizador,
        UserName = identityName
    });
}
```

---

## Paso 3 — Frontend: modal

```html
<div id="ModalAutorizacion" class="modal fade">
    <div class="modal-dialog">
        <div class="modal-content">
            <div class="modal-header">
                <h4>Autorización requerida</h4>
            </div>
            <div class="modal-body">
                <div class="form-group">
                    <label>Contraseña:</label>
                    <input type="password" id="passwordAutorizacion" class="form-control" />
                </div>
                <div class="form-group">
                    <label>Comentario:</label>
                    <textarea id="comentarioAutorizacion" class="form-control"></textarea>
                </div>
            </div>
            <div class="modal-footer">
                <button class="btn btn-default" data-dismiss="modal">Cerrar</button>
                <button class="btn btn-success" data-bind="click: mensajeConfirmacion">Autorizar</button>
            </div>
        </div>
    </div>
</div>
```

---

## Paso 4 — Frontend: lógica

```javascript
function validarAutorizacion(tipoAutorizador) {
    var password = $("#passwordAutorizacion").val();
    var comentario = $("#comentarioAutorizacion").val();

    if (!password.trim()) {
        toastr.error("Favor de teclear la contraseña");
        return;
    }
    if (!comentario.trim()) {
        toastr.error("Favor de ingresar el comentario");
        return;
    }

    servicio.Catalogo.ValidarAutorizacion({
        password: password,
        tipoAutorizador: tipoAutorizador
    }).done(function (d) {
        if (d.Error === 1) {
            toastr.error(d.Mensaje);
            return;
        }

        // 1. Guardar el rol real
        data.TipoAutorizador = d.TipoAutorizadorReal;
        data.UserNameAutorizador = d.UserName;
        data.Comentario = "Autorizado por " + d.TipoAutorizadorReal + ": " + comentario;

        // 2. Cerrar modal
        $('#ModalAutorizacion').modal('hide');

        // 3. Continuar el flujo
        guardarData(data);
    });
}
```

---

## Paso 5 — Backend: guardar en bitácora

```csharp
if (item.NivelAutorizacion > 0)
{
    var bitacora = new BitacoraInfo
    {
        NivelAutorizacion = item.NivelAutorizacion,
        TipoAutorizador = item.TipoAutorizador,
        UserNameAutorizador = item.UserNameAutorizador,
        Comentario = item.Comentario,
        UserNameEjecutivo = identityName
    };
    bitacoraDAO.Insertar(bitacora);
}
```

---

## Reglas de oro

1. **Validar rol primero, después contraseña.**
2. **Super usuario autoriza cualquier rango.**
3. **Devolver rol real**, no el solicitado.
4. **Guardar en bitácora** con todos los datos.
5. **Roles en `Rol` + `PermisoUsuarioRol`**, no hardcodeados.

---

## ✅ Checklist

- [ ] Rol creado en tabla `Rol`
- [ ] Rol asignado a los usuarios
- [ ] `ValidarAutorizacion` en el controller
- [ ] Modal de autorización
- [ ] Validación de password
- [ ] Guardar en bitácora con rol real
- [ ] Probado localmente

---

*Última actualización: 2026-10-14*