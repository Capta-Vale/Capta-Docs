# Guía: Crear un reporte

> Cómo agregar un nuevo reporte en Capta.Credivoucher.

---

## Cuándo usar

- El usuario necesita exportar datos.
- Se requiere PDF o Excel.
- El reporte puede ser simple o complejo.

---

## Tipos de reporte

| Tipo | Tecnología | Cuándo |
|---|---|---|
| **RDLC** | SQL Server Reporting Services | Reportes con diseño visual |
| **Excel** | NPOI / ClosedXML / EPPlus | Exportaciones tabulares |
| **PDF** | iTextSharp | Documentos formales |
| **CSV** | CsvHelper | Datos simples |
| **Background** | `ReporteSegundoPlanHelper` | Reportes >5 segundos |

---

## Opción A — Reporte Excel simple

### Paso 1 — Crear el Manager

```csharp
public class ReporteManager
{
    public MemoryStream ExportarExcel(ReporteFilter filter)
    {
        var dao = new DomainObjects.ReporteDomainObjects();
        var datos = dao.ObtenerDatos(filter);

        using (var workbook = new XLWorkbook())
        {
            var sheet = workbook.Worksheets.Add("Datos");

            // Encabezados
            sheet.Cell(1, 1).Value = "Cliente";
            sheet.Cell(1, 2).Value = "Monto";

            // Datos
            int row = 2;
            foreach (var item in datos)
            {
                sheet.Cell(row, 1).Value = item.Cliente;
                sheet.Cell(row, 2).Value = item.Monto;
                row++;
            }

            var stream = new MemoryStream();
            workbook.SaveAs(stream);
            stream.Position = 0;
            return stream;
        }
    }
}
```

### Paso 2 — Crear el Controller

```csharp
[Securirty.Imp.CustomAuthorizationFilter(Contracts.Enums.Permisos.Lectura)]
public JsonResult ExportarExcel(ReporteFilter filter)
{
    var stream = _manager.ExportarExcel(filter);
    var fileName = $"Reporte_{DateTime.Now:yyyyMMdd_HHmmss}.xlsx";
    var key = Session["reporteKey"] as string ?? Guid.NewGuid().ToString();
    Session[key] = stream.ToArray();

    return Json(new { Error = 0, FileName = key + "|" + fileName });
}

public FileResult DownloadFile(string key)
{
    var bytes = Session[key] as byte[];
    Session.Remove(key);
    var fileName = key.Split('|')[1];
    return File(bytes, "application/vnd.openxmlformats-officedocument.spreadsheetml.sheet", fileName);
}
```

### Paso 3 — Crear el JS

```javascript
function exportarExcel() {
    var filter = armarFiltro();

    servicio.Reporte.ExportarExcel(filter).done(function (d) {
        if (d.Error === 1) {
            toastr.error(d.Mensaje);
            return;
        }
        window.location = action.Reporte.DownloadFile + '?key=' + d.FileName;
    });
}
```

---

## Opción B — Reporte PDF con RDLC

### Paso 1 — Crear el reporte `.rdlc`

Diseñar en Visual Studio.

### Paso 2 — Crear el Manager

```csharp
public byte[] GenerarPdf(ReporteFilter filter)
{
    var datos = dao.ObtenerDatos(filter);

    var reportViewer = new LocalReport();
    reportViewer.ReportPath = HttpContext.Current.Server.MapPath("~/Reports/Reporte.rdlc");
    reportViewer.DataSources.Add(new ReportDataSource("DataSet1", datos));

    return reportViewer.Render("PDF");
}
```

### Paso 3 — Crear el Controller

```csharp
public FileResult DownloadPdf(ReporteFilter filter)
{
    var bytes = _manager.GenerarPdf(filter);
    return File(bytes, "application/pdf", "Reporte.pdf");
}
```

---

## Opción C — Reporte en background (>5 segundos)

### Paso 1 — Usar `ReporteSegundoPlanHelper`

```csharp
public JsonResult IniciarReporte(ReporteFilter filter)
{
    var key = ReporteSegundoPlanHelper.Start(User.Identity.Name, () =>
        Task.Run(() => _manager.GenerarReporte(filter)));

    return Json(new { Error = 0, Key = key });
}

public JsonResult ConsultarEstatus(string key)
{
    var estatus = ReporteSegundoPlanHelper.GetEstatus(key, User.Identity.Name);
    return Json(new { Error = 0, Terminado = estatus.Terminado });
}

public FileResult DescargarReporte(string key)
{
    var bytes = ReporteSegundoPlanHelper.Take(key, User.Identity.Name);
    return File(bytes, "application/pdf", "Reporte.pdf");
}
```

### Paso 2 — JS con polling

```javascript
function generarReporte() {
    var filter = armarFiltro();

    servicio.Reporte.IniciarReporte(filter).done(function (d) {
        if (d.Error === 1) { toastr.error(d.Mensaje); return; }

        var key = d.Key;
        var polling = setInterval(function () {
            servicio.Reporte.ConsultarEstatus(key).done(function (r) {
                if (r.Terminado) {
                    clearInterval(polling);
                    window.location = action.Reporte.DescargarReporte + '?key=' + key;
                }
            });
        }, 2000);
    });
}
```

---

## ✅ Checklist

- [ ] Tipo de reporte elegido
- [ ] Manager creado
- [ ] Controller creado
- [ ] JS creado
- [ ] Permisos configurados
- [ ] Probado localmente
- [ ] Reporte >5 seg va en background

---

*Última actualización: 2026-10-14*