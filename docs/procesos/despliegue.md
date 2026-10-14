# Despliegue

> Proceso de despliegue a QA y Producción.

---

## 🎯 Objetivo

Desplegar cambios de forma segura y controlada.

---

## 🚦 Ambientes

| Ambiente | Propósito | URL |
|---|---|---|
| **Local** | Desarrollo | localhost |
| **QA** | Pruebas | qa.captavale.com |
| **Producción** | Real | captavale.com |

---

## 📋 Pre-despliegue

- [ ] PR aprobado y mergeado a `main`.
- [ ] Build genera sin errores.
- [ ] Tests pasan.
- [ ] Scripts SQL listos.
- [ ] Backup de BD de QA hecho.
- [ ] Ventana de mantenimiento (si aplica).

---

## 🚀 Despliegue a QA

### Paso 1 — Build

```powershell
msbuild Capta.Credivoucher.sln /p:Configuration=Release
```

### Paso 2 — Copiar binarios

Copiar a `\\qa-server\Capta\`:

- `Capta.WebApplication`
- `Capta.Service.*`
- `Capta.*.Api`

### Paso 3 — Ejecutar scripts SQL

```sql
-- Ejecutar en orden los scripts pendientes
-- Scripts/Tables/[CAPTA]-*.sql
```

### Paso 4 — Reiniciar IIS

```powershell
iisreset
```

### Paso 5 — Smoke tests

- [ ] La app abre
- [ ] Login funciona
- [ ] Flujos críticos funcionan

---

## 🚀 Despliegue a Producción

**Requiere aprobación de la Líder de Desarrollo.**

### Paso 1 — Backup de BD

```sql
BACKUP DATABASE CaptaQA
TO DISK = 'C:\Backups\CaptaQA_20261014.bak'
WITH INIT, COMPRESSION;
```

### Paso 2 — Backup de binarios

Copiar la carpeta actual a `\\prod-server\Backups\`.

### Paso 3 — Copiar binarios

Copiar los nuevos binarios.

### Paso 4 — Ejecutar scripts SQL

Ejecutar scripts pendientes.

### Paso 5 — Reiniciar IIS

```powershell
iisreset
```

### Paso 6 — Monitoreo

Durante 15 minutos, monitorear:

- Logs de errores.
- Uso de CPU/memoria.
- Conexiones activas.

### Paso 7 — Rollback (si es necesario)

Restaurar backup de binarios y BD.

---

## 🕐 Ventanas de despliegue

| Ambiente | Ventana |
|---|---|
| QA | Cualquier hora |
| Producción | Fuera de horario (20:00-22:00) |

---

## ✅ Checklist

- [ ] PR mergeado
- [ ] Build exitoso
- [ ] Tests pasan
- [ ] Scripts SQL listos
- [ ] Backup hecho
- [ ] Binarios copiados
- [ ] Scripts ejecutados
- [ ] IIS reiniciado
- [ ] Smoke tests OK
- [ ] Monitoreo 15 min

---

*Última actualización: 2026-10-14*