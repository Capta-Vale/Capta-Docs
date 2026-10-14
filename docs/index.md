# Manual de Desarrollo — Capta.Credivoucher


> **Documentación institucional del sistema.** Reglas, estándares y guías que todo desarrollador debe conocer.

---

## 🎯 Propósito

Este manual define **cómo se construye software en Capta.Credivoucher**. Su objetivo es:

1. **Evitar que nuevos desarrolladores rompan lo establecido.**
2. **Estandarizar la forma de trabajar** en todos los módulos.
3. **Documentar las decisiones arquitectónicas** y sus fundamentos.
4. **Servir como referencia** para code review y QA.

---

## 📖 ¿Por dónde empezar?

### 👋 Soy nuevo en el proyecto

1. [Bienvenida](empezar/bienvenida.md) — Qué es Capta, qué hacemos.
2. [Setup local](empezar/setup-local.md) — Cómo montar el proyecto en tu máquina.
3. [Tu primer cambio](empezar/primer-cambio.md) — Guía paso a paso.
4. [Onboarding completo](procesos/onboarding.md) — Plan de 2 semanas.

### 💻 Voy a hacer un cambio

1. Revisa los [Estándares](estandares/nombres.md) — Cómo se escribe el código.
2. Sigue la [Guía específica](guias/crear-endpoint.md) — Cómo hacer X.
3. Lee las [Reglas de negocio](reglas/negocio.md) — Qué se puede y qué no.
4. Cumple el [Proceso de code review](procesos/code-review.md).

### 🏛️ Quiero entender la arquitectura

1. [Visión general](arquitectura/vision-general.md)
2. [Capas](arquitectura/capas.md)
3. [Patrones](arquitectura/patrones.md)
4. [Decisiones arquitectónicas](arquitectura/decisiones.md)
5. [Antipatrones](arquitectura/antipatrones.md) — Lo que NO se debe hacer.

### 📊 Quiero ver diagramas

- [Índice de diagramas](diagramas/README.md)
- [Diagramas básicos](diagramas/basicos.md)
- [Diagramas avanzados](diagramas/avanzados.md)

### 🗺️ Quiero ver el mapeado técnico

- [Mapeado completo del sistema](mapeados/index.md) — Análisis detallado de arquitectura, rutas, dependencias, BD, navegación y documentación.

---

## 📚 Secciones del manual

| Sección | Contenido |
|---|---|
| **[Empezar](empezar/bienvenida.md)** | Onboarding, setup, primer cambio |
| **[Arquitectura](arquitectura/vision-general.md)** | Cómo está construido el sistema |
| **[Estándares](estandares/nombres.md)** | Cómo se escribe el código |
| **[Guías](guias/crear-endpoint.md)** | Cómo hacer tareas comunes |
| **[Procesos](procesos/onboarding.md)** | Cómo se trabaja en el equipo |
| **[Reglas](reglas/negocio.md)** | Reglas de negocio institucionales |
| **[Referencia](referencia/estructura-solucion.md)** | Estructura, BD, integraciones |
| **[Diagramas](diagramas/README.md)** | Colección de diagramas Mermaid |
| **[Mapeados](mapeados/index.md)** | Análisis técnico detallado |
| **[Apéndices](apendices/glosario.md)** | Glosario, FAQ, contacto |

---

## ⚠️ Reglas de oro

Estas 7 reglas resumen toda la filosofía del manual:

1. **No inventes.** Si algo ya existe, úsalo. Si no existe, pregunta antes.
2. **No rompas.** Antes de modificar algo, entiende por qué está así.
3. **No dupliques.** Si vas a copiar código, extrae un patrón.
4. **Documenta.** Si tomas una decisión no obvia, deja un comentario.
5. **Prueba.** Antes de subir, verifica que funciona.
6. **Revisa.** Antes de mergear, pasa por code review.
7. **Reporta.** Si encuentras un bug, no lo arregles sin avisar.

---

## 🛠️ Stack del sistema

| Capa | Tecnología |
|---|---|
| **Backend** | ASP.NET MVC 5 (.NET Framework 4.8) |
| **ORM** | EntityFramework 6 + BLToolkit 4 + Dapper 1.60 |
| **Base de Datos** | SQL Server 2019 |
| **Frontend** | KnockoutJS + Bootstrap 3 + jQuery 2.1 + Kendo UI |
| **Reportes** | RDLC + iTextSharp + NPOI |
| **Autenticación** | ASP.NET Identity + Azman |
| **Servicios** | 6 Windows Services + 3 Web APIs |

---

## 👥 Equipo

| Rol | Responsabilidad |
|---|---|
| **Comité de Arquitectura** | Aprueba decisiones, mantiene este manual |
| **Líder de Desarrollo** | Karina Gpe. Peinado Aguilar |
| **Gerente de Operación** | Marissa L. Izurieta Ramírez |

---

## 🔄 Versión

| Campo | Valor |
|---|---|
| **Versión** | 1.0 |
| **Última actualización** | 2026-10-14 |
| **Próxima revisión** | 2027-01-14 |

---

*Este manual es un documento vivo. Si encuentras algo desactualizado o incorrecto, repórtalo al Comité de Arquitectura.*
