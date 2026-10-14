# Guía: Crear servicio Windows

> Cómo crear un nuevo servicio Windows en Capta.Credivoucher.

---

## Cuándo usar

- Proceso batch que corre en background.
- Proceso que dura más de 5 minutos.
- Proceso periódico (diario, mensual).

---

## Estructura

```
Capta.Service.Nombre/
├── Program.cs
├── Service1.cs
├── ProjectInstaller.cs
├── App.config
└── Properties/
    └── AssemblyInfo.cs
```

---

## Paso 1 — Crear el proyecto

**En Visual Studio:**

1. **File → New → Project**.
2. **Windows Service (.NET Framework)**.
3. Nombre: `Capta.Service.Nombre`.
4. Framework: **.NET Framework 4.8**.

---

## Paso 2 — Agregar referencias

Agregar referencias a:

- `Capta.Core`
- `Capta.Contracts`
- `Capta.Model`
- `log4net`

---

## Paso 3 — Configurar `Program.cs`

```csharp
static class Program
{
    static void Main()
    {
        ServiceBase[] ServicesToRun;
        ServicesToRun = new ServiceBase[]
        {
            new ServiceNombre()
        };
        ServiceBase.Run(ServicesToRun);
    }
}
```

---

## Paso 4 — Configurar el servicio

`ServiceNombre.cs`:

```csharp
public partial class ServiceNombre : ServiceBase
{
    private Timer _timer;
    private readonly ILog _log = LogManager.GetLogger(typeof(ServiceNombre));

    public ServiceNombre()
    {
        InitializeComponent();
    }

    protected override void OnStart(string[] args)
    {
        _log.Info("Servicio iniciado");

        // Ejecutar cada hora
        _timer = new Timer(3600000);
        _timer.Elapsed += OnTimerElapsed;
        _timer.AutoReset = true;
        _timer.Enabled = true;

        // Ejecutar inmediatamente al iniciar
        OnTimerElapsed(null, null);
    }

    protected override void OnStop()
    {
        _log.Info("Servicio detenido");
        _timer?.Dispose();
    }

    private void OnTimerElapsed(object sender, ElapsedEventArgs e)
    {
        try
        {
            _log.Info("Ejecutando proceso...");

            var manager = new NombreManager();
            manager.Procesar();

            _log.Info("Proceso completado");
        }
        catch (Exception ex)
        {
            _log.Error("Error en proceso", ex);
        }
    }
}
```

---

## Paso 5 — Crear el installer

`ProjectInstaller.cs`:

```csharp
[RunInstaller(true)]
public partial class ProjectInstaller : System.Configuration.Install.Installer
{
    public ProjectInstaller()
    {
        InitializeComponent();

        var processInstaller = new ServiceProcessInstaller
        {
            Account = ServiceAccount.LocalSystem
        };

        var serviceInstaller = new ServiceInstaller
        {
            ServiceName = "Capta.Service.Nombre",
            DisplayName = "Capta - Nombre",
            Description = "Descripción del servicio",
            StartType = ServiceStartMode.Automatic
        };

        Installers.Add(processInstaller);
        Installers.Add(serviceInstaller);
    }
}
```

---

## Paso 6 — Configurar `App.config`

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <connectionStrings>
    <add name="ModelCoreConnection"
         connectionString="Server=...;Database=CaptaQA;..."
         providerName="System.Data.SqlClient" />
  </connectionStrings>

  <configSections>
    <section name="log4net" type="log4net.Config.Log4NetConfigurationSectionHandler, log4net" />
  </configSections>

  <log4net>
    <appender name="FileAppender" type="log4net.Appender.RollingFileAppender">
      <file value="C:\Logs\Capta.Service.Nombre.log" />
      <appendToFile value="true" />
      <rollingStyle value="Date" />
      <datePattern value="yyyyMMdd" />
      <layout type="log4net.Layout.PatternLayout">
        <conversionPattern value="%date [%thread] %-5level %logger - %message%newline" />
      </layout>
    </appender>

    <root>
      <level value="INFO" />
      <appender-ref ref="FileAppender" />
    </root>
  </log4net>
</configuration>
```

---

## Paso 7 — Instalar el servicio

```powershell
# En PowerShell como administrador
cd C:\Windows\Microsoft.NET\Framework64\v4.0.30319

# Instalar
.\InstallUtil.exe "C:\ruta\a\Capta.Service.Nombre.exe"

# Iniciar
Start-Service "Capta.Service.Nombre"

# Verificar
Get-Service "Capta.Service.Nombre"
```

---

## Paso 8 — Desinstalar

```powershell
Stop-Service "Capta.Service.Nombre"
.\InstallUtil.exe /u "C:\ruta\a\Capta.Service.Nombre.exe"
```

---

## Reglas de oro

1. **Logging obligatorio** con log4net.
2. **Try/catch** alrededor del proceso principal.
3. **Timer** para ejecuciones periódicas.
4. **Nombres descriptivos** para el servicio.
5. **Configuración en `App.config`**.

---

## ✅ Checklist

- [ ] Proyecto creado (.NET Framework 4.8)
- [ ] Referencias agregadas
- [ ] `Program.cs` configurado
- [ ] Servicio con `OnStart`/`OnStop`
- [ ] Installer configurado
- [ ] `App.config` con log4net
- [ ] Probado localmente
- [ ] Instalado en servidor
- [ ] Documentado el propósito

---

*Última actualización: 2026-10-14*