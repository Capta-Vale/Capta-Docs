# Tu primer cambio

> Guía paso a paso para hacer tu primer cambio en Capta.Credivoucher.

---

## Ejemplo del cambio

Vas a **agregar un campo `Celular` a la entidad `Cliente`** y mostrarlo en la pantalla de Clientes.

Este ejemplo cubre **todas las capas**, para que entiendas el flujo completo.

---

## Antes de empezar

- [ ] Repositorio clonado y compilando.
- [ ] Setup local completado.
- [ ] Ticket asignado en Tiaga (ej. `#999`).

---

## Paso 1 — Crear la rama

```powershell
git checkout main
git pull origin main
git checkout -b feature/999-Capta-Agregar-Celular-Cliente
```

**Convención:** `prefijo/folio-proyecto-descripcion`

- Prefijo: `feature`, `fix`, `hotfix`, `bugfix`, `refactor`
- Folio: número de Tiaga
- Proyecto: `Capta` o `Pagos`
- Descripción: kebab-case

---

## Paso 2 — Modificar la base de datos

**Regla:** primero la BD, después el código.

Abre SSMS en `CaptaQA` y ejecuta:

```sql
ALTER TABLE Cliente
ADD Celular NVARCHAR(20) NULL;
GO
```

**Guarda el script** en `Scripts/Tables/` del repositorio:

```
Scripts/Tables/[CAPTA]-Agregar-Celular-Cliente.sql
```

---

## Paso 3 — Actualizar la entidad

Abre `Capta.Model/Context/Core/Entity/Cliente.cs`:

```csharp
public class Cliente : Isi.LRFramework.Entidad
{
    // ... propiedades existentes ...
    public string Celular { get; set; }
}
```

**Regla:** los nombres de propiedades coinciden exactamente con los campos de la BD.

---

## Paso 4 — Actualizar el Info

Abre `Capta.Contracts/Info/ClienteInfo.cs`:

```csharp
public class ClienteInfo
{
    // ... propiedades existentes ...
    public string Celular { get; set; }
}
```

**Regla:** el `Info` debe tener **todos** los campos que necesita el frontend.

---

## Paso 5 — Verificar el DAO

Abre `Capta.Core/Integration/DAO/ClienteDAO.cs`. Si el DAO usa `MapInfo`, **no hay que cambiar nada** (el mapeo es automático):

```csharp
public ClienteInfo ObtenerPorId(int id)
{
    var query = Db.Cliente.Where(a => a.Id == id);
    var qSelect = from a in query
                  select MapInfo<Entity.Cliente, ClienteInfo>(a);
    return qSelect.SingleOrDefault();
}
```

**Si el DAO hace mapeo manual**, hay que agregar el campo.

---

## Paso 6 — Actualizar la vista

Abre `Capta.WebApplication/Views/Cliente/Create.cshtml` y agrega:

```html
<div class="form-group">
    @LRHtml.LabelAndString("Celular", etiqueta: "Celular", longitud: 20)
</div>
```

---

## Paso 7 — Actualizar el JavaScript

Abre `Capta.WebApplication/Views/Cliente/Create.js`:

```javascript
var modelo = {
    Id: ko.observable(),
    Nombre: ko.observable(),
    Celular: ko.observable("")
};
```

---

## Paso 8 — Probar localmente

1. Compila (`Ctrl+Shift+B`).
2. Ejecuta el proyecto (`F5`).
3. Navega a la pantalla de Cliente.
4. Crea un cliente con celular.
5. Verifica en la BD que se guardó.

**Checklist de pruebas:**

- [ ] El campo se muestra en la vista.
- [ ] El campo se guarda en la BD.
- [ ] El campo se carga al editar un cliente existente.
- [ ] El campo se valida (longitud, formato).
- [ ] No hay errores en la consola del navegador.

---

## Paso 9 — Commit y push

```powershell
git add .
git commit -m "feat(999-Capta): agregar campo Celular a Cliente

- ALTER TABLE Cliente
- Campo en Entity y Info
- Campo en vista Create
- Campo en ViewModel JS

Refs: TIAGA-999"

git push origin feature/999-Capta-Agregar-Celular-Cliente
```

**Convención de commits:** ver [estándares/commits.md](../estandares/commits.md).

---

## Paso 10 — Pull Request

1. Abre GitHub en el repositorio.
2. Verás el banner de "Compare & pull request".
3. Llena el template:
   - **Título:** `feat(999-Capta): agregar campo Celular a Cliente`
   - **Descripción:** qué cambia, por qué, cómo probar.
   - **Ticket:** link a Tiaga.
   - **Screenshots:** si es UI.
4. Asigna a un revisor.
5. Espera aprobación.

---

## Paso 11 — Code review

El revisor validará:

- [ ] El código sigue los [estándares](../estandares/codigo-cs.md).
- [ ] El cambio no rompe nada existente.
- [ ] Los tests (si aplican) pasan.
- [ ] El cambio está documentado.

**Durante el review:**

- Responde los comentarios uno por uno.
- Haz los ajustes solicitados.
- Vuelve a hacer push (el PR se actualiza automáticamente).

---

## Paso 12 — Merge

Una vez aprobado:

1. El revisor o tú hacen **Squash and merge**.
2. Se elimina la rama.
3. Se cierra el ticket en Tiaga.

---

## ✅ Checklist completo

- [ ] Rama creada con la convención correcta
- [ ] Script SQL versionado
- [ ] Entidad actualizada
- [ ] Info actualizado
- [ ] DAO revisado
- [ ] Vista actualizada
- [ ] JavaScript actualizado
- [ ] Probado localmente
- [ ] Commit con mensaje correcto
- [ ] Push a origin
- [ ] Pull Request creado
- [ ] Code review aprobado
- [ ] Merge hecho
- [ ] Ticket cerrado

---

## 🎉 ¡Listo!

Acabas de hacer tu primer cambio siguiendo el flujo completo.

**Siguiente:** [Onboarding completo](../procesos/onboarding.md) para profundizar.

---

## 🐛 Problemas comunes

### El campo no se guarda

- Verifica que el `Info` y la `Entity` tengan la propiedad.
- Verifica que la tabla tenga la columna.

### El campo no se muestra

- Verifica que el JS tenga el `ko.observable`.
- Verifica que la vista esté bindeada.

### El PR tiene conflictos

- `git pull origin main`
- Resuelve conflictos manualmente.
- `git add .` y `git commit`
- `git push`

---

*Última actualización: 2026-10-14*