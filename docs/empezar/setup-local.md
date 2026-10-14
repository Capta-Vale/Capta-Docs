# Setup local

> Cómo montar Capta.Credivoucher en tu máquina de desarrollo.

---

## Requisitos previos

| Software | Versión | Para qué |
|---|---|---|
| Visual Studio 2022 | 17.0+ | IDE principal |
| .NET Framework | 4.8 | Runtime del proyecto |
| SQL Server | 2019+ | Base de datos local |
| SQL Server Management Studio | 19.0+ | Administración de BD |
| Git | 2.40+ | Control de versiones |
| NuGet | Incluido en VS | Gestión de paquetes |

---

## Paso 1 — Clonar el repositorio

```powershell
cd C:\Proyectos
git clone https://github.com/Capta-Vale/Capta.Credivoucher.git
cd Capta.Credivoucher
```

---

## Paso 2 — Configurar credenciales de Git

```powershell
git config user.name "Tu Nombre"
git config user.email "tu-correo@captasoluciones.com"
```

---

## Paso 3 — Restaurar paquetes NuGet

**Opción A — Desde Visual Studio:**

1. Abre `Capta.Credivoucher.sln`.
2. Click derecho sobre la solución → **Restore NuGet Packages**.

**Opción B — Desde PowerShell:**

```powershell
nuget restore Capta.Credivoucher.sln
```

---

## Paso 4 — Configurar cadenas de conexión

Abre `Capta.WebApplication/Web.config` y ajusta:

```xml
<connectionStrings>
  <add name="ModelCoreConnection"
       connectionString="Server=localhost;Database=CaptaQA;Integrated Security=true;"
       providerName="System.Data.SqlClient" />
</connectionStrings>
```

> ⚠️ Nunca commitear credenciales reales. Usa `localhost` con autenticación de Windows.

---

## Paso 5 — Restaurar la base de datos

1. Solicita el backup de `CaptaQA` al equipo de BD.
2. Restaura el backup en tu SQL Server local.
3. Verifica la conexión desde SSMS.

**Bases de datos del sistema:**

| Base de datos | Propósito |
|---|---|
| `CaptaQA` | Core del negocio (~350 tablas) |
| `DatawareHouseDB` | Analítica y reportes |
| `SavingDB` | Ahorro e inversiones |
| `DatosAnalizadosDB` | OCR y análisis IA |

Para desarrollo local, con `CaptaQA` es suficiente.

---

## Paso 6 — Compilar la solución

**Desde Visual Studio:**

1. **Build → Rebuild Solution** (Ctrl+Shift+B).
2. Verifica que no haya errores.

**Desde PowerShell:**

```powershell
msbuild Capta.Credivoucher.sln /p:Configuration=Debug
```

**Errores comunes:**

| Error | Solución |
|---|---|
| `Reference not found` | Restaurar NuGet, verificar versión de .NET |
| `OpenXmlCompositeElement` | Verificar versión de `DocumentFormat.OpenXml` en el csproj |
| `DUPLICATE class` | Verificar archivos con BOM o duplicados |

---

## Paso 7 — Configurar IIS Express

Visual Studio configura IIS Express automáticamente. Verifica:

1. Click derecho en `Capta.WebApplication` → **Properties**.
2. **Web** → **Project Url**: `http://localhost:44300/`.

---

## Paso 8 — Ejecutar

1. En Visual Studio, selecciona `Capta.WebApplication` como proyecto de inicio.
2. Presiona **F5**.
3. Se abre el navegador con la pantalla de login.

**Credenciales de prueba:** solicitar al líder de desarrollo.

---

## Paso 9 — Configurar herramientas adicionales

### Extensiones recomendadas de Visual Studio

| Extensión | Para qué |
|---|---|
| ReSharper (opcional) | Análisis de código |
| Visual Assist | Productividad |
| EditorConfig | Formato consistente |
| Git Diff Margin | Ver cambios de Git |

---

## ✅ Checklist de setup

- [ ] Repositorio clonado
- [ ] Credenciales de Git configuradas
- [ ] NuGet restaurado
- [ ] Cadenas de conexión configuradas
- [ ] Backup de `CaptaQA` restaurado
- [ ] Solución compila sin errores
- [ ] IIS Express configurado
- [ ] App abre correctamente en el navegador
- [ ] Login funciona

---

## 🐛 Problemas comunes

### No compila

1. Borra `bin/` y `obj/` de todos los proyectos.
2. Restaura NuGet.
3. Rebuild completo.

### No conecta a la BD

1. Verifica el servidor en SSMS.
2. Verifica el connection string.
3. Verifica que el usuario tenga permisos.

### El diseño se ve roto

1. Limpia el caché del navegador (Ctrl+Shift+R).
2. Verifica que los assets CSS/JS se carguen sin 404.

---

## 🚀 Siguiente paso

→ [Tu primer cambio](primer-cambio.md)