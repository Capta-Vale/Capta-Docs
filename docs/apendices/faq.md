# FAQ

> Preguntas frecuentes.

---

## 🛠️ Desarrollo

### ¿Cómo hago para clonar el repo?

Ver [Setup local](../empezar/setup-local.md).

### ¿Qué framework de frontend usan?

KnockoutJS + Bootstrap 3 + jQuery + Kendo UI.

### ¿Por qué usan 3 ORMs?

Historia del proyecto. Ver [ADR-001](../arquitectura/decisiones.md).

### ¿Cómo agrego un nuevo endpoint?

Ver [Guía: crear endpoint](../guias/crear-endpoint.md).

### ¿Cómo agrego una tabla?

Ver [Guía: crear tabla](../guias/crear-tabla.md).

### ¿Cómo hago un reporte?

Ver [Guía: crear reporte](../guias/crear-reporte.md).

---

## 🔐 Seguridad

### ¿Cómo agrego un nuevo permiso?

Ver [Guía: agregar autorización](../guias/agregar-autorizacion.md) y [Permisos](../reglas/permisos.md).

---

## 🚀 Despliegue

### ¿Cómo despliego a QA?

Ver [Proceso de despliegue](../procesos/despliegue.md).

### ¿Cómo hago un hotfix?

Ver [Proceso de hotfix](../procesos/hotfix.md).

---

## 🐛 Problemas comunes

### No compila

- Borrar `bin/` y `obj/`.
- Restaurar NuGet.
- Rebuild.

### Error de OpenXml

- Verificar versión en el `.csproj`.
- Debe ser 3.5.1.0.

### Archivo con BOM en el nombre

- Eliminar el archivo duplicado.
- Verificar con PowerShell.

### DBContext null

- Verificar que el Manager esté instanciado.
- Verificar que el DomainObject abra el DBContext.

---

## 📚 Documentación

### ¿Dónde está la documentación?

En este manual. Y en GitHub: `/docs`.

### ¿Cómo contribuyo a la documentación?

- Editar el archivo `.md` correspondiente.
- Hacer PR.
- Code review.

---

*Última actualización: 2026-10-14*