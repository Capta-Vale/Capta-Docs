# Estándar de código JavaScript

> Reglas de escritura de JavaScript en Capta.Credivoucher.

---

## 🎯 Principios

1. **Sin lógica de negocio en el cliente.**
2. **Toda validación se revalida en el backend.**
3. **Usar Knockout para reactividad.**
4. **No duplicar la lógica del servidor.**

---

## 📏 Formato

- 4 espacios de indentación.
- Punto y coma al final de cada sentencia.
- Comillas simples para strings.
- No `var`, usar `let` o `const`.

---

## 🔴 Reglas obligatorias

### JS-01 — `const` por defecto, `let` cuando se reasigne

```javascript
// Correcto
const total = 100;
let contador = 0;
contador++;

// Incorrecto
var total = 100;   // ← var no se usa
```

### JS-02 — No `==` / `!=`, usar `===` / `!==`

```javascript
// Correcto
if (valor === 5) { }

// Incorrecto
if (valor == 5) { }   // ← coerción implícita
```

### JS-03 — Funciones con menos de 50 líneas

Dividir si excede.

### JS-04 — Sin lógica de negocio

Las reglas van en el backend. El JS solo valida formato y envía al servidor.

### JS-05 — No `alert()`

Usar `toastr` o `SweetAlert2`.

```javascript
toastr.success("Guardado correctamente", "Éxito");
Swal.fire({ title: '¿Confirmar?', showCancelButton: true });
```

### JS-06 — No `console.log` en producción

Eliminar antes de commitear.

### JS-07 — Nombres en `camelCase`

```javascript
const clienteActual = ...;
function procesarRespuesta(data) { }
```

### JS-08 — `ko.observable` para estado

```javascript
var vm = {
    nombre: ko.observable(""),
    total: ko.observable(0)
};
```

### JS-09 — `ko.mapping.fromJS` para datos del servidor

```javascript
var vm = ko.mapping.fromJS(modelo);
```

### JS-10 — Bindings en el HTML, no en el JS

**Correcto:**

```html
<input data-bind="value: nombre" />
```

**Incorrecto:**

```javascript
document.getElementById("nombre").value = vm.nombre();
```

---

## 🎨 Estilo

### Comillas

```javascript
// Correcto
const mensaje = 'Hola';

// Incorrecto
const mensaje = "Hola";
```

### Template literals

```javascript
// Correcto
const url = `/Cliente/Obtener/${id}`;

// Incorrecto
const url = "/Cliente/Obtener/" + id;
```

### Arrow functions

```javascript
// Correcto
const dobles = numeros.map(n => n * 2);

// Incorrecto (cuando no es necesario)
const dobles = numeros.map(function (n) { return n * 2; });
```

### Null checking

```javascript
// Correcto
if (!cliente) return;
const nombre = cliente?.nombre ?? "Sin nombre";
```

---

## 🚫 Antipatrones

| Antipatrón | Correcto |
|---|---|
| `var` | `const` o `let` |
| `==` / `!=` | `===` / `!==` |
| `alert()` | `toastr` o `Swal.fire` |
| `console.log` en commit | Eliminar |
| Manipulación DOM directa | `data-bind` de Knockout |
| Lógica de negocio en JS | Moverla al backend |
| Funciones anónimas largas | Extraer a función nombrada |
| Callbacks anidados | Promises o async/await |

---

## ✅ Checklist

- [ ] `const` por defecto, `let` cuando se reasigne
- [ ] Sin `var`
- [ ] `===` / `!==` en lugar de `==` / `!=`
- [ ] Sin `alert()`
- [ ] Sin `console.log` en producción
- [ ] Bindings con `data-bind` de Knockout
- [ ] Nombres en `camelCase`
- [ ] Sin lógica de negocio
- [ ] Funciones <50 líneas
- [ ] Sin callbacks anidados (usar `async/await`)

---

*Última actualización: 2026-10-14*