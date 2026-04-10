---
name: agree
description: >
  Automatización completa del flujo Git: analiza cambios, crea rama, hace commits atómicos,
  pushea, crea PR y mergea. Nunca trabaja en main directo. Siempre pregunta en puntos clave.
  Trigger: When user runs /agree or asks to commit, push, create PR, or do the full git flow.
license: Apache-2.0
metadata:
  author: gentleman-programming
  version: "1.0"
---

## When to Use

- Usuario ejecuta `/agree`
- Pide "hacé el commit", "subí los cambios", "creá el PR", "mergea"
- Cualquier variante del flujo completo de Git

---

## Flujo Completo — Seguir en Orden

### PASO 1: Verificar estado

```bash
git status --porcelain
git diff --stat
git branch --show-current
git remote -v
```

**Decisiones:**
| Situación | Acción |
|-----------|--------|
| No hay cambios | Decir "No hay cambios" y terminar |
| No hay remoto | Preguntar URL o seguir solo con commits locales |
| Ya está en rama de feature | Preguntar si continuar ahí o crear nueva |
| Está en main | Crear rama nueva (NUNCA commitear en main) |

---

### PASO 2: Preguntar al usuario

Mostrar estado y preguntar:

```
📍 Estás en: {rama}
📝 Tenés {N} archivos cambiados

❓ ¿Qué estuviste haciendo?
   (Contame qué cambios hiciste)
```

Esperar respuesta. No asumir nada.

---

### PASO 3: Detectar tipo desde la descripción

| Palabras clave | Tipo |
|----------------|------|
| "agregué", "creé", "nuevo", "implementé", "añadí" | `feat` |
| "arreglé", "corregí", "bug", "fix", "error" | `fix` |
| "refactoricé", "migré", "reestructuré", "moví" | `refactor` |
| "documentación", "readme", "docs", "comenté" | `docs` |
| "formateé", "prettier", "estilos", "lint" | `style` |
| "tests", "test", "testing", "cobertura" | `test` |
| "dependencias", "config", "actualicé paquetes" | `chore` |

Si describe múltiples cosas → múltiples commits.

Confirmar antes de actuar:

```
✅ Detectado:
   - Tipo: {tipo}
   - Rama a crear: {tipo}/{nombre-kebab}

   Commits que voy a hacer:
   1. {tipo1}: {descripción1}
   2. {tipo2}: {descripción2}

❓ ¿Confirmás? [s/n]
```

---

### PASO 4: Crear rama (si está en main)

```bash
git checkout -b {tipo}/{nombre-en-kebab-case}
```

**Reglas para el nombre:**
- Todo minúsculas
- Espacios → guiones
- Sin tildes ni caracteres especiales
- Ejemplo: "agregar login JWT" → `feat/agregar-login-jwt`

---

### PASO 5: Commits atómicos — EL MÁS IMPORTANTE

Agrupar archivos por tipo y hacer un commit por grupo.

**Orden obligatorio:**
1. `test:` — tests primero (TDD)
2. `feat:`/`fix:`/`refactor:` — lógica principal
3. `chore:` — dependencias y configuración
4. `docs:` — documentación al final

**Clasificación de archivos:**
| Archivos | Tipo de commit |
|----------|----------------|
| `tests/`, `*.test.*`, `__tests__/`, `spec/` | `test:` |
| `src/`, `lib/`, `app/`, `*.ts`, `*.py`, `*.go` | `feat:` / `fix:` / `refactor:` |
| `package.json`, `*.lock`, `requirements.txt`, `go.mod` | `chore:` |
| `.env*`, `*.config.*`, `Dockerfile*`, `*.yaml` | `chore:` |
| `README.md`, `docs/`, `*.md` | `docs:` |
| `*.css`, `*.scss`, `styles/` | `style:` |

```bash
# Ejemplo de commits atómicos
git add tests/
git commit -m "test: agregar tests para {feature}"

git add src/
git commit -m "feat: agregar {feature}"

git add package.json
git commit -m "chore: agregar dependencias para {feature}"
```

Verificar al final:
```bash
git log --oneline -5
```

---

### PASO 6: Preguntar qué hacer después

```
✅ Commits hechos ({N} commits)

❓ ¿Qué hacemos ahora?
   [1] Push + PR + Merge a main     (todo el flujo, terminamos)
   [2] Push + PR                    (subo y creo PR, sin mergear)
   [3] Solo Push                    (solo subo la rama)
   [4] Solo Commits                 (solo commits locales, nada más)
```

**ESPERAR** respuesta del usuario. No continuar sin confirmar.

---

### PASO 7: Ejecutar la opción elegida

**Opción [1] Push + PR + Merge:**
```bash
git push -u origin {rama}
gh pr create --title "{tipo}: {descripción}" --body "$(cat <<'EOF'
## Resumen
{descripción}

## Cambios
{lista de archivos modificados}

## Commits
{lista de commits}
EOF
)"
gh pr merge --squash --delete-branch --auto
git checkout main
git pull origin main
```

**Opción [2] Push + PR:**
```bash
git push -u origin {rama}
gh pr create --title "{tipo}: {descripción}" --body "..."
```

**Opción [3] Solo Push:**
```bash
git push -u origin {rama}
```

**Opción [4] Solo Commits:**
Solo reportar los commits hechos. Nada más.

---

### PASO 8: Reporte final

**Si hubo merge:**
```
✅ /agree completado!

📦 Integración a main:
   Rama: {rama}
   Commits: {N} (squashed a 1)

🔗 PR: {url-del-pr}
📍 Main: actualizado y en sync
```

**Si solo PR:**
```
✅ PR creado!
🔗 PR: {url-del-pr}
💡 Para mergear, ejecutá /agree de nuevo.
```

**Si solo push:**
```
✅ Rama pusheada!
🔗 Rama: {url-de-la-rama}
```

**Si solo commits:**
```
✅ Commits locales listos:
   {lista de commits}
💡 Para pushear, ejecutá /agree de nuevo.
```

---

## Manejo de Errores

| Error | Respuesta |
|-------|-----------|
| No hay remoto | Preguntar URL o continuar solo con commits |
| `gh` no instalado | Mostrar URL manual para crear PR |
| Rama ya existe en remoto | Preguntar: forzar push / usar existente / nuevo nombre |
| Conflictos en merge | Preguntar: resolver manualmente / cancelar merge |
| Sin permisos | Verificar `gh auth status` |

---

## Reglas Invariables

1. **NUNCA commitear en main directamente** — siempre crear rama
2. **NUNCA hacer todo en un solo commit** — commits atómicos por tipo
3. **SIEMPRE confirmar** antes de crear rama, commitear y mergear
4. **SIEMPRE squash merge** — main queda limpio
5. **SIEMPRE borrar la rama** después del merge (`--delete-branch`)
6. **NO usar `--force`** en push a main salvo que el usuario lo pida explícitamente con advertencia
