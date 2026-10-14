# Comandos útiles

> Comandos PowerShell y SQL que se usan seguido.

---

## Git

```powershell
# Crear rama
git checkout -b feature/232-Capta-Descripcion

# Ver estado
git status

# Ver commits recientes
git log --oneline -10

# Actualizar con main
git fetch origin
git rebase origin/main

# Deshacer último commit (sin perder cambios)
git reset --soft HEAD~1

# Ver diferencias
git diff

# Ver quién cambió una línea
git blame archivo.cs
```

---

## Build

```powershell
# Compilar solución
msbuild Capta.Credivoucher.sln /p:Configuration=Debug

# Rebuild
msbuild Capta.Credivoucher.sln /t:Rebuild

# Restaurar NuGet
nuget restore Capta.Credivoucher.sln
```

---

## SQL

```sql
-- Buscar una tabla
SELECT name FROM sys.tables WHERE name LIKE '%Cliente%';

-- Buscar una columna
SELECT t.name AS Tabla, c.name AS Columna
FROM sys.columns c
JOIN sys.tables t ON c.object_id = t.object_id
WHERE c.name LIKE '%FechaRegistro%';

-- Ver la definición de un SP
EXEC sp_helptext 'sp_GetCliente';

-- Ver índices de una tabla
EXEC sp_helpindex 'Cliente';

-- Ver tamaño de tablas
SELECT 
    t.name AS Tabla,
    SUM(p.rows) AS Filas,
    SUM(a.total_pages) * 8 / 1024 AS MB
FROM sys.tables t
JOIN sys.indexes i ON t.object_id = i.object_id
JOIN sys.partitions p ON i.object_id = p.object_id AND i.index_id = p.index_id
JOIN sys.allocation_units a ON p.partition_id = a.container_id
WHERE i.index_id <= 1
GROUP BY t.name
ORDER BY MB DESC;
```

---

## IIS

```powershell
# Reiniciar IIS
iisreset

# Ver estado de un sitio
Get-Website -Name "capta-web"

# Ver logs
Get-Content "C:\inetpub\logs\LogFiles\W3SVC1\*.log" -Tail 50
```

---

## Servicios Windows

```powershell
# Listar servicios de Capta
Get-Service | Where-Object { $_.Name -like "Capta*" }

# Iniciar
Start-Service "Capta.Service.CustomerLock"

# Detener
Stop-Service "Capta.Service.CustomerLock"

# Ver logs
Get-Content "C:\Logs\Capta.Service.CustomerLock.log" -Tail 100
```

---

## Logs

```powershell
# Logs de la app
Get-Content "C:\Logs\Capta.WebApplication.log" -Tail 100

# Buscar errores
Select-String -Path "C:\Logs\*.log" -Pattern "ERROR"

# Logs de IIS
Get-Content "C:\inetpub\logs\LogFiles\W3SVC1\*.log" -Tail 100
```

---

*Última actualización: 2026-10-14*