# Code review

> Cómo hacer y recibir code review en Capta.Credivoucher.

---

## 🎯 Objetivo

Asegurar que todo cambio cumple los estándares antes de llegar a `main`.

---

## 👀 Para el revisor

### Qué revisar

- [ ] **Funcionalidad:** ¿el cambio hace lo que dice?
- [ ] **Estándares:** ¿sigue los estándares de código?
- [ ] **Arquitectura:** ¿respeta las capas?
- [ ] **Antipatrones:** ¿evita los antipatrones?
- [ ] **Reglas de negocio:** ¿cumple las reglas?
- [ ] **Tests:** ¿están cubiertos los casos?
- [ ] **Documentación:** ¿actualizó los docs?
- [ ] **Seguridad:** ¿valida permisos?

### Cómo comentar

**Bien:**

> ¿Podrías extraer esta lógica a un método? Tiene más de 100 líneas.

**Mal:**

> Esto está mal.

**Regla:** comentar el "qué" y el "por qué", no el "quién".

### Tiempos

- **Cambios pequeños:** <2 horas.
- **Cambios medianos:** <1 día.
- **Cambios grandes:** <2 días.

---

## 📝 Para el autor

### Antes de pedir review

- [ ] El código compila.
- [ ] Los tests pasan.
- [ ] Probado localmente.
- [ ] Sin `console.log` ni `debugger`.
- [ ] Commits atómicos.
- [ ] Descripción clara en el PR.

### Durante el review

- [ ] Responder cada comentario.
- [ ] No tomarlo personal.
- [ ] Si no estás de acuerdo, argumentar.
- [ ] Hacer los cambios solicitados.

### Después

- [ ] Hacer los ajustes.
- [ ] Push de los cambios.
- [ ] Notificar al revisor.

---

## ✅ Aprobación

**1 aprobación** para cambios pequeños.

**2 aprobaciones** para cambios que afectan:

- Reglas de negocio.
- Seguridad.
- Base de datos.
- APIs públicas.

---

## 🚫 Qué NO es code review

- ❌ Revisar el estilo (lo hace el linter).
- ❌ Revisar si compila (lo hace CI).
- ❌ Revisar si pasa tests (lo hace CI).

**El code review se enfoca en:**

- Lógica.
- Arquitectura.
- Mantenibilidad.
- Reglas de negocio.

---

*Última actualización: 2026-10-14*