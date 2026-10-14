# Estándar de vistas Razor

> Reglas de escritura de vistas `.cshtml` en Capta.Credivoucher.

---

## 🎯 Principios

1. Las vistas solo presentan datos.
2. Los bindings de Knockout van en el HTML.
3. No contener lógica de negocio.
4. Usar los helpers `@LRHtml.*` para consistencia.

---

## 🔴 Reglas obligatorias

### CSHTML-01 — Título de página obligatorio

```html
<h1 class="page-title">@ViewBag.Title</h1>
```

### CSHTML-02 — Usar `@LRHtml.*` para campos

```html
@LRHtml.LabelAndString("Nombre", etiqueta: "Nombre", longitud: 100)
@LRHtml.LabelAndMoneyFor("LimiteCredito", etiqueta: "Límite de Crédito", longitud: 10)
```

**No inventar HTML de campos.**

### CSHTML-03 — `data-bind` para Knockout

```html
<input data-bind="value: nombre" />
<select data-bind="options: lista, value: seleccionado"></select>
```

### CSHTML-04 — Ids en camelCase

```html
<input id="nombreCliente" />
```

### CSHTML-05 — No lógica de negocio

Si necesitas condicionales complejos, van en el JS o en el backend.

### CSHTML-06 — Usar partials para lo repetido

```html
@{ Html.RenderPartial("~/Views/Cliente/_GridCliente.cshtml"); }
```

### CSHTML-07 — `@section Scripts` al final

```html
@section Scripts {
    <script src="~/Scripts/Cliente/Index.js"></script>
}
```

### CSHTML-08 — `@` duplicado en CSS embebido

```html
<style>
    @@media (max-width: 768px) { }
    @@keyframes spin { }
</style>
```

---

## 🎨 Estructura de una vista

```html
@{
    ViewBag.Title = "Nombre de la pantalla";
}

<div class="container-fluid">
    <h1 class="page-title">@ViewBag.Title</h1>

    <div class="row">
        <div class="col-md-12">
            <!-- Contenido -->
        </div>
    </div>
</div>

@section Scripts {
    <script src="~/Scripts/Modulo/Index.js"></script>
}
```

---

## 🎨 Estructura de un parcial

```html
<div id="ModalCliente" class="modal fade" role="dialog">
    <div class="modal-dialog">
        <div class="modal-content">
            <div class="modal-header">
                <h4 class="modal-title">Cliente</h4>
            </div>
            <div class="modal-body">
                <!-- Contenido -->
            </div>
            <div class="modal-footer">
                <button class="btn btn-default" data-dismiss="modal">Cerrar</button>
            </div>
        </div>
    </div>
</div>
```

**Nota:** los parciales **no** llevan `@section` (no funcionan en parciales).

---

## 🚫 Antipatrones

| Antipatrón | Correcto |
|---|---|
| HTML de campos a mano | Usar `@LRHtml.*` |
| Lógica de negocio en vista | Moverla al backend |
| `@` suelto en CSS | Duplicar a `@@` |
| Ids en PascalCase | Usar camelCase |
| JavaScript inline | Archivo `.js` separado |
| Estilos inline | CSS en archivo |

---

## ✅ Checklist

- [ ] `@ViewBag.Title` definido
- [ ] Campos con `@LRHtml.*`
- [ ] `data-bind` de Knockout
- [ ] Ids en `camelCase`
- [ ] Sin lógica de negocio
- [ ] Partials para lo repetido
- [ ] `@section Scripts` al final
- [ ] `@@` duplicado en CSS

---

*Última actualización: 2026-10-14*