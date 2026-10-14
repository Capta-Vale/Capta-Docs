# Reglas de Negocio Institucionales

> **Reglas transversales del sistema Capta.Credivoucher.** Aplican a todos los módulos. Cualquier excepción debe ser validada por Operación.

---

## 📖 Filosofía

Estas reglas definen **cómo se comporta el negocio**, no cómo se implementa. Son inmutables sin aprobación de Operación.

**Cada módulo nuevo debe:**

1. Cumplir TODAS estas reglas.
2. Documentar las reglas adicionales específicas de su dominio.
3. No contradecir ninguna regla institucional.

---

## 🔐 A. Autorización

### RN-A01 — Autorización escalonada por impacto

> Toda operación que afecte el patrimonio requiere autorización escalonada según el impacto.

**Fundamento:** ninguna decisión con impacto económico puede ser tomada por una sola persona.

**Aplica a:** cambios de límites, condonaciones, reestructuraciones, ajustes manuales.

**Implementación:** cada módulo define sus umbrales, todos usan el motor común de autorización.

---

### RN-A02 — Roles configurables

> Los roles de autorización se administran por configuración, no por código.

**Implementación:** tablas `Rol` + `PermisoUsuarioRol`.

---

### RN-A03 — Super usuario autoriza cualquier rango

> Un autorizador de nivel superior puede autorizar cualquier operación de un nivel inferior.

---

### RN-A04 — Doble factor de autorización

> Toda autorización requiere contraseña + comentario justificado.

---

### RN-A05 — Registro del autorizador real

> Toda autorización se registra con el rol real de quien firmó, no con el rol solicitado.

---

## 🔄 B. Estados y transiciones

### RN-B01 — Transiciones unidireccionales

> Una vez que un objeto alcanza un estado terminal, no puede volver a un estado activo sin autorización especial.

---

### RN-B02 — Rastro de transiciones

> Todo cambio de estado queda registrado con usuario, fecha, estado anterior y estado nuevo.

---

### RN-B03 — Estados bloqueantes controlados

> Los estados que bloquean al usuario solo se aplican por proceso automático o autorización de nivel superior.

---

### RN-B04 — Estados mutuamente excluyentes

> Un objeto no puede estar en dos estados simultáneos.

---

## 💰 C. Cálculos financieros

### RN-C01 — Precisión decimal

> Los montos siempre se calculan con decimales; nunca con enteros ni punto flotante.

---

### RN-C02 — Orden de aplicación de pagos

> Los pagos se aplican en orden: **capital → intereses → mora**.

---

### RN-C03 — Tope de pago al saldo pendiente

> Un pago no puede exceder el saldo pendiente del crédito.

---

### RN-C04 — Tasa congelada

> Los cálculos usan la tasa vigente al momento de la operación.

---

### RN-C05 — Mora por día

> La mora se calcula por día de atraso, no por periodo completo.

---

## 📊 D. Cartera y riesgo

### RN-D01 — Límite basado en capacidad

> El límite de crédito se basa en la capacidad de pago demostrable.

---

### RN-D02 — Alcance calculado por el sistema

> El alcance potencial se calcula con datos del sistema, no se ingresa manualmente.

---

### RN-D03 — Cambios de proyección limitados

> Los cambios de proyección se limitan por porcentaje sobre el alcance o el valor anterior.

**Rangos:**

| Cambio | Autorización |
|---|---|
| 0–10% | Ejecutivo (directo) |
| 11–29% | Gerente |
| 30–50% | Gerente Regional |
| >50% | Supervisor |

---

### RN-D04 — Topes temporales para nuevos

> Los objetos nuevos pueden tener límites más estrictos durante un periodo inicial.

---

### RN-D05 — Bloqueo automático por adeudo

> Los adeudos vencidos se bloquean automáticamente sin intervención manual.

---

### RN-D06 — Dependencias bloquean operaciones

> Un objeto con dependencias activas no puede eliminarse ni darse de baja.

---

## 📝 E. Auditoría y trazabilidad

### RN-E01 — Rastro obligatorio de operaciones sensibles

> Toda operación que modifique el patrimonio o los datos críticos deja rastro en bitácora.

---

### RN-E02 — Bitácora inmutable

> La bitácora nunca se modifica ni se elimina; solo se agrega.

---

### RN-E03 — Valor anterior y nuevo

> La bitácora registra el valor anterior y el nuevo.

---

### RN-E04 — Contexto completo

> La bitácora incluye usuario, fecha, motivo y contexto.

---

### RN-E05 — Notificación de operaciones críticas

> Toda operación crítica genera notificación a los interesados.

---

## ⚙️ F. Operación

### RN-F01 — Catálogos administrables

> Los catálogos se administran sin necesidad de despliegue.

---

### RN-F02 — Notificaciones no bloqueantes

> Los envíos de notificación no bloquean el flujo principal.

---

### RN-F03 — Fallos externos aislados

> Los fallos de servicios externos no rompen el flujo del sistema.

---

### RN-F04 — Procesos masivos en background

> Los procesos que afectan a muchos registros se ejecutan en background.

---

### RN-F05 — Datos sensibles enmascarados

> Los datos sensibles se enmascaran en logs y pantallas cuando no son indispensables.

---

## 🔄 Cómo agregar una regla nueva

1. **Identificar la categoría** (A, B, C, D, E, F).
2. **Asignar código único** (ej. `RN-D07`).
3. **Describir la regla en una línea.**
4. **Documentar el fundamento.**
5. **Validar con Operación.**

---

## 📌 Reglas específicas por módulo

Viven en `docs/modulos/<modulo>.md`. Extienden las institucionales pero no las contradicen.

---

*Última actualización: 2026-10-14*
*Responsable: Comité de Arquitectura + Operación*