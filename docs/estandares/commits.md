# Estándar de commits y ramas

> Convención de Git en Capta.Credivoucher.

---

## 🌿 Ramas

### Formato

```
prefijo/folio-proyecto-descripcion
```

### Prefijos

| Prefijo | Uso | Ejemplo |
|---|---|---|
| `feature/` | Nueva funcionalidad | `feature/232-Capta-Candado-Supervisor` |
| `fix/` | Corrección de bug no crítico | `fix/315-Capta-Calculo-Mora` |
| `bugfix/` | Corrección de bug (sinónimo) | `bugfix/315-Capta-Calculo-Mora` |
| `hotfix/` | Corrección urgente | `hotfix/316-Capta-Session-Timeout` |
| `refactor/` | Refactor sin cambio funcional | `refactor/317-Capta-DAO-Modernizacion` |
| `docs/` | Documentación | `docs/318-Capta-MkDocs-Setup` |
| `feedback/` | Retroalimentación | `feedback/319-Capta-Ajuste-UI` |
| `chore/` | Mantenimiento | `chore/320-Capta-Actualizar-NuGet` |
| `test/` | Solo tests | `test/321-Capta-Tests-Analisis-Credito` |

### Ejemplos

```
feature/232-Capta-Candado-Supervisor
fix/315-Capta-Calculo-Mora
hotfix/316-Capta-Session-Timeout
docs/318-Capta-MkDocs-Setup
```

---

## 📝 Commits

### Formato

```
prefijo(folio-proyecto): descripción corta en imperativo

[cuerpo opcional]

[footer opcional con referencias]
```

### Prefijos de commit

| Prefijo | Uso |
|---|---|
| `feat` | Nueva funcionalidad |
| `fix` | Corrección de bug |
| `hotfix` | Corrección urgente |
| `refactor` | Refactor |
| `docs` | Documentación |
| `test` | Tests |
| `chore` | Mantenimiento |

### Ejemplos

```
feat(232-Capta): agregar rol Supervisor de Proyección

- Nuevo rol en tabla Rol
- Validación en ValidarGerente
- Bitácora de autorizaciones

Refs: TIAGA-232
```

```
fix(314-Capta): corregir NullReferenceException en ValidarGerente

El campo _adminManager no estaba inicializado.

Refs: TIAGA-314
```

```
docs(318-Capta): agregar documentación MkDocs

- Estructura de carpetas
- mkdocs.yml configurado
- Reglas institucionales

Refs: TIAGA-318
```

---

## 🔀 Flujo de trabajo

### 1. Crear rama desde `main`

```powershell
git checkout main
git pull origin main
git checkout -b feature/232-Capta-Candado-Supervisor
```

### 2. Trabajar y commitear

```powershell
git add .
git commit -m "feat(232-Capta): ..."
```

### 3. Mantener actualizada con main

```powershell
git fetch origin
git rebase origin/main
```

### 4. Push

```powershell
git push origin feature/232-Capta-Candado-Supervisor
```

### 5. Pull Request

- Crear PR en GitHub.
- Asignar revisor.
- Esperar aprobación.

### 6. Merge

- **Squash and merge** (una sola línea en `main`).
- Eliminar la rama.

---

## 🚫 Antipatrones

| Antipatrón | Correcto |
|---|---|
| Commitear `bin/`, `obj/` | Agregar al `.gitignore` |
| Commitear archivos con BOM en el nombre | Renombrar |
| Commits gigantes | Commits atómicos por cambio lógico |
| Mensajes vagos (`fix`, `update`) | Mensaje descriptivo |
| Mergear sin review | Siempre PR + review |
| Force push a `main` | Nunca |
| Commits sin folio | Siempre con folio de Tiaga |

---

## ✅ Checklist

- [ ] Rama con formato correcto
- [ ] Commits atómicos
- [ ] Mensaje descriptivo en imperativo
- [ ] Folio de Tiaga referenciado
- [ ] Sin archivos `bin/`, `obj/`
- [ ] Rebase con `main` antes de PR
- [ ] PR creado y asignado
- [ ] Code review aprobado
- [ ] Squash and merge

---

*Última actualización: 2026-10-14*