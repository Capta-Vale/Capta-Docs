# QA

> Proceso de Quality Assurance en Capta.Credivoucher.

---

## 🎯 Objetivo

Asegurar que los cambios no rompen funcionalidad existente.

---

## 📋 Tipos de pruebas

### 1. Pruebas de humo (smoke tests)

Después del despliegue a QA:

- [ ] La app abre.
- [ ] El login funciona.
- [ ] Las pantallas principales cargan.
- [ ] Los flujos críticos funcionan.

### 2. Pruebas funcionales

Casos de prueba por módulo.

### 3. Pruebas de regresión

Verificar que los cambios no rompieron nada.

### 4. Pruebas de integración

Probar con servicios externos.

---

## 🧪 Cómo documentar bugs

**Formato:**

```markdown
## Bug #123 — Error al guardar cliente sin RFC

**Pasos para reproducir:**
1. Ir a Cliente → Nuevo
2. Llenar datos sin RFC
3. Presionar Guardar

**Resultado esperado:** guarda con RFC vacío

**Resultado obtenido:** error 500

**Evidencia:** [screenshot]

**Ambiente:** QA / Producción

**Severidad:** Alta
```

---

## 🎯 Severidades

| Severidad | Descripción | Tiempo de respuesta |
|---|---|---|
| **Crítica** | Afecta producción | Inmediato |
| **Alta** | Afecta funcionalidad | <1 día |
| **Media** | Afecta UX | <3 días |
| **Baja** | Cosmético | <1 semana |

---

## 🚀 Automatización (futuro)

**Estado actual:** QA manual.

**Roadmap 2026:**

- Tests unitarios (30% cobertura).
- Tests de integración.
- Tests E2E con Playwright.

---

## ✅ Checklist de QA

- [ ] Pruebas de humo después de despliegue
- [ ] Casos de prueba documentados
- [ ] Bugs reportados con formato
- [ ] Retroalimentación al equipo

---

*Última actualización: 2026-10-14*