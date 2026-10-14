# Hotfix

> Proceso de corrección urgente en Producción.

---

## 🎯 Cuándo usar

- Bug crítico en Producción.
- Afecta al negocio.
- No puede esperar al siguiente ciclo.

---

## 🚦 Flujo

### 1. Confirmar que es crítico

- Afecta a usuarios.
- Afecta al negocio (dinero, datos).
- No hay workaround.

### 2. Avisar al líder

- Notificar a Karina Peinado.
- Obtener aprobación.

### 3. Crear rama de hotfix

```powershell
git checkout main
git pull origin main
git checkout -b hotfix/999-Capta-Descripcion
```

### 4. Hacer el cambio mínimo

- Solo el fix necesario.
- Sin refactor.
- Sin cambios colaterales.

### 5. Probar localmente

- Verificar que el bug se resuelve.
- Verificar que no rompe nada.

### 6. PR urgente

- Título: `HOTFIX: <descripción>`
- Asignar a 2 revisores.
- Esperar aprobación rápida.

### 7. Merge y despliegue

- Squash and merge.
- Desplegar a Producción.
- Smoke tests inmediatos.

### 8. Monitoreo

- 30 minutos de monitoreo intensivo.
- Verificar logs.
- Verificar que el bug está resuelto.

### 9. Post-mortem

- Documentar qué pasó.
- Cómo se detectó.
- Cómo se arregló.
- Cómo prevenirlo.

---

## ⏱️ Tiempos

| Paso | Tiempo |
|---|---|
| Confirmar crítico | 5 min |
| Crear rama | 2 min |
| Hacer el fix | Variable |
| PR + review | 15 min |
| Despliegue | 15 min |
| Monitoreo | 30 min |
| **Total** | **~1 hora** |

---

## 🚫 Reglas

- **Solo el fix**, sin extras.
- **2 revisores** mínimo.
- **Despliegue inmediato** tras aprobación.
- **Monitoreo post-despliegue.**
- **Post-mortem obligatorio.**

---

## ✅ Checklist

- [ ] Confirmado como crítico
- [ ] Líder notificado
- [ ] Rama creada con `hotfix/`
- [ ] Fix mínimo
- [ ] Probado localmente
- [ ] PR con 2 revisores
- [ ] Desplegado a Producción
- [ ] Monitoreo 30 min
- [ ] Post-mortem documentado

---

*Última actualización: 2026-10-14*