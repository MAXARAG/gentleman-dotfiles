---
description: Automatizar git: crear rama, commits atómicos, push, crear PR y merge a main (checkout -b | add + commit | push | pr create | pr merge --squash)
agent: gentleman
---

# /agree — Automatización Completa de Buenas Prácticas Git

## Qué hace este comando

Automatiza el flujo completo de Git para que no tengas que ejecutar múltiples comandos manualmente.

**Problema que resuelve:**
- ¿Te olvidás de crear una rama antes de pushear?
- ¿Commiteás todo en un solo commit gigante?
- ¿Nunca creás Pull Requests?
- ¿Directamente pusheás a main?

**Solución:** Un solo comando que hace TODO: analiza cambios, crea rama, commitea bien, pushea, crea PR, mergea y limpia.

**Un solo comando. Buenas prácticas garantizadas.**

---

## Paso 1: Verificar si hay cambios

Antes de hacer cualquier cosa, hay que verificar si el usuario tiene cambios sin commitlear.

### Por qué es importante

Si ejecutás `/agree` y no hay cambios, no tiene sentido crear una rama, no hay nada que pushear. Sería como intentar enviar un快递 cuando no hay nada en la caja.

### Los comandos que se ejecutan

```bash
git status --porcelain
git diff --stat
git branch --show-current
```

| Comando | Para qué | Qué te dice |
|---------|----------|-------------|
| `git status --porcelain` | Estado en formato limpio | Muestra los archivos cambiados de forma compacta |
| `git diff --stat` | Resumen de cambios | Cuántas líneas agregaste/borraste |
| `git branch --show-current` | En qué rama estás | Si estás en main o en otra rama |

### Qué hago con esa información

- **Si no hay cambios:** Le digo al usuario "No hay cambios para subir" y termino ahí.
- **Si hay cambios:** Continúo con el flujo normalmente.
- **Si ya está en una rama de feature:** Uso esa rama en lugar de crear una nueva.
- **Si está en main:** Creo una nueva rama de feature (nunca trabajo directo en main).

### Verificar si hay remoto configurado

Antes de continuar, verifico si el proyecto tiene un remoto configurado:

```bash
git remote -v
```

| Resultado | Qué hago |
|-----------|----------|
| Hay remoto (origin) | Continúo normalmente |
| No hay remoto | Pregunto cómo proceder |

---

### Si NO hay remoto configurado

**Yo te pregunto:**
```
⚠️ Este proyecto no tiene un remoto configurado.

   git remote -v
   (vacío)

   ¿Qué querés hacer?
   [1] Pasame la URL del repo (crear remoto y pushear)
   [2] Solo hacer commits locales (sin remoto)
   [3] Cancelar
```

**Si elegís [1]:**
```
❓ Pasame la URL del repo (ej: https://github.com/usuario/mi-app)
```

**Vos:** "https://github.com/usuario/mi-app"

**Yo:**
```bash
git remote add origin https://github.com/usuario/mi-app
git push -u origin main  # Si tenés commits locales en main
```

→ Después de agregar el remoto, continúo con el flujo normal.

**Si elegís [2]:**
→ Solo hago los commits (opción [4] Solo Commits)
→ Te aviso: "⚠️ Recordá que no hay remoto. Cuando quieras pushear, primero agregá el repo."

---

## Paso 2: Preguntar qué hizo el usuario

Esta es la parte INTERACTIVA. Vos me contás lo que hiciste y yo determino TODO: el tipo, el nombre de la rama, y cómo separar los commits.

### Escenario A: Estás en main (rama nueva)

**Yo te pregunto:**
```
📍 Estás en: main
📝 Tenés X archivos cambiados sin commitear

❓ ¿Qué estuviste haciendo?
   (Contame en español qué cambios hiciste)
```

**Vos me decís:** "Estuve agregando login con Google, con tests ybmodifiqué el package"

**Yo detecto y te confirmo:**
```
✅ Detectado:
   - Tipo: feat (agregaste algo nuevo)
   - Rama a crear: feat/agregar-login-google
   
   Commits que voy a hacer:
   1. test: agregar tests para login
   2. feat: agregar login con Google
   3. chore: actualizar dependencias

❓ ¿Confirmás? [s/n]
```

---

### Escenario B: Ya estás en una rama (continuar ahí)

**Yo te pregunto:**
```
📍 Estás en: feat/mi-rama-existente (tiene 3 commits)

❓ ¿Querés continuar desde acá o crear una nueva rama?
   [1] Continuar en feat/mi-rama-existente
   [2] Crear nueva rama
```

**Vos:** "Continuar"

**Yo:**
```
✅ Continuamos en feat/mi-rama-existente

📝 Tenés X archivos cambiados sin commitear

❓ ¿Qué cambios hiciste desde el último commit?
```

**Vos:** "Agregué validación de email y tests"

**Yo detecto:**
```
✅ Detectado:
   - Tipo: feat (agregaste validación)
   - Tipo: test (agregaste tests)
   
   Commits que voy a hacer:
   1. test: agregar tests para validación de email
   2. feat: agregar validación de email

❓ ¿Confirmás? [s/n]
```

---

### Cómo determino el tipo desde tu descripción

Analizo las palabras que me decís:

| Palabras que decís | Tipo detectado |
|--------------------|----------------|
| "agregué", "creé", "nuevo", "implementé" | **feat** |
| "arreglé", "corregí", "bug", "fix" | **fix** |
| "refactoricé", "migré", "reestructuré" | **refactor** |
| "documentación", "leí", "leeme" | **docs** |
| "formateé", "prettier", "estilos" | **style** |
| "tests", "testear", "coverage" | **test** |
| "dependencias", "config", "actualicé" | **chore** |

Si me decís varias cosas (ej: "agregué login y tests"), detecto MULTIPLES tipos y hago MULTIPLES commits.

---

## Paso 4: Crear la rama de feature (si corresponde)

Una vez que tengo la descripción, genero el nombre de la rama y la creo.

### Por qué siempre crear una rama

Trabajar en `main` directo es una MALA PRÁCTICA. Si rompes algo en main, rompés la versión de producción.

La rama es tu "sandbox" — un espacio seguro para experimentar sin afectar a nadie más.

### El comando que se ejecuta

```bash
git checkout -b {tipo}/{nombre-kebab}
```

### Cómo genero el nombre

1. **Convierto a kebab-case** (todo minúsculas, guiones en vez de espacios)
   - "agregar autenticación JWT" → "agregar-autenticacion-jwt"

2. **Agrego el prefijo del tipo**
   - feat → feature
   - fix → fix
   - refactor → refactor
   - docs → docs
   - chore → chore
   - test → test
   - style → style

3. **Ejemplos completos:**
   - "agregar autenticación JWT" → `feat/agregar-autenticacion-jwt`
   - "corregir error de login" → `fix/corregir-error-de-login`
   - "refactorizar servicio de usuarios" → `refactor/refactorizar-servicio-de-usuarios`
   - "actualizar dependencias" → `chore/actualizar-dependencias`

### Qué hago después de crearla

- Verifico que estoy parado en la nueva rama: `git branch --show-current`
- Listo para hacer commits

---

## Paso 5: Commits atómicos (EL MÁS IMPORTANTE)

Este es el corazón del comando. En lugar de un solo commit gigante, separo los cambios en grupos lógicos.

### Por qué es importante

**Malo (un solo commit):**
```
git add -A
git commit -m "agregué login y tests y actualicé package"
```

**Bueno (commits atómicos):**
```
git add tests/
git commit -m "test: agregar tests para login"

git add src/
git commit -m "feat: agregar login con Google"

git add package.json
git commit -m "chore: actualizar dependencias"
```

### Cómo agrupo los archivos

Analizo los archivos que cambiaste con `git diff --stat` y los agrupo por tipo:

| Tipo de archivo | Grupo | Tipo de commit |
|-----------------|-------|----------------|
| `tests/**`, `*.test.ts`, `__tests__/**` | Tests | `test:` |
| `src/**`, `lib/**`, `app/**`, `*.ts`, `*.js` | Lógica principal | `feat:`, `fix:`, `refactor:` |
| `package.json`, `*.lock`, `requirements.txt` | Dependencias | `chore:` |
| `.env*`, `*.config.*`, `Dockerfile*` | Configuración | `chore:` |
| `README.md`, `docs/**`, `*.md` | Documentación | `docs:` |
| `*.css`, `*.scss`, `styles/**` | Estilos | `style:` |

### Ejemplo concreto

**Tus cambios:**
```
Modified:   src/auth/service.ts       (+50 líneas)
Modified:   src/auth/middleware.ts   (+20 líneas)
Modified:   tests/auth.test.ts       (+100 líneas)
Modified:   package.json              (+5 líneas)
Modified:   README.md                 (+10 líneas)
```

**Yo hago:**
```bash
# Commit 1: Tests (primero, porque son la especificación)
git add tests/auth.test.ts
git commit -m "test: agregar tests para autenticación"

# Commit 2: Lógica principal
git add src/auth/service.ts src/auth/middleware.ts
git commit -m "feat: agregar autenticación JWT"

# Commit 3: Configuración
git add package.json
git commit -m "chore: agregar dependencias jsonwebtoken y bcrypt"

# Commit 4: Documentación (último)
git add README.md
git commit -m "docs: actualizar guía de autenticación"
```

### La regla de orden

1. **Primero los tests** — si hay tests, van primero (TDD)
2. **Segundo la lógica** — el código que hace funcionar la feature
3. **Tercero configuración** — dependencias, env, configs
4. **Último documentación** — README, comments

### Si solo hay un grupo de archivos

Si todos tus cambios son de un solo tipo, hago un solo commit:
```
git add -A
git commit -m "feat: agregar login con Google"
```

No complico si no es necesario.

### Qué hago después de los commits

- Verifico con `git log --oneline -5` que quedaron bien
- Listo para pushear

---

## Paso 6: Pushear la rama al remoto

Subir la rama a GitHub para que esté disponible para crear el PR.

### El comando

```bash
git push -u origin {nombre-rama}
```

El `-u` (o `--set-upstream`) vincula la rama local con la remota. Esto significa que las próximas veces solo necesitás `git push` sin especificar la rama.

### Por qué es importante pushear ANTES de crear el PR

- El PR se crea desde la rama remota (no la local)
- Permite que GitHub muestre el diff automáticamente
-others pueden ver tu trabajo antes de que se mergee

### Si la rama ya existe en el remoto

Si ya había un remote con ese nombre:
```bash
git push origin {nombre-rama}
```

(sin -u porque ya está vinculado)

### Qué hago después de pushear

- Verifico con `git remote -v` que quedó vinculado
- Listo para crear el PR

---

## Paso 7: Crear el Pull Request

El PR es la "pregunta formal" para meter tu código en main. Es donde Happens la revisión.

### El comando (usando GitHub CLI)

```bash
gh pr create \
  --title "{tipo}: {descripción}" \
  --body "{descripción automática}" \
  --base main \
  --head {nombre-rama}
```

### Qué contiene el PR

**Título automático:**
- "feat: agregar login con Google"
- "fix: corregir error de validación"
- etc.

**Cuerpo automático:**
```markdown
## Resumen
{descripción de lo que hiciste}

## Cambios
- src/auth/service.ts - lógica de autenticación
- tests/auth.test.ts - tests de autenticación
- package.json - nuevas dependencias

## Commits
- test: agregar tests para autenticación
- feat: agregar login con Google
- chore: agregar dependencias

## Cómo probar
1. npm install
2. npm run dev
3. Probar el endpoint POST /auth/login
```

### Por qué hacer un PR (aunque seas solo vos)

1. **Historial limpio** — el merge queda documentado
2. **Reversible** — si algo rompe, podés revertir el merge
3. **Disciplina** — te obliga a revisar tu propio código antes de mergear
4. **Automático** — con squash merge, main queda con un solo commit limpio

### Si NO tenés gh (GitHub CLI)

Si no está instalado, te muestro el link manual:
```
⚠️ GitHub CLI no está instalado.

Creá el PR manualmente en:
https://github.com/{usuario}/{repo}/compare/main...{rama}

Instalá con: winget install GitHub.cli
```

### Después de crear el PR

- Guardo la URL del PR
- Listo para hacer el merge

---

## Paso 8: Preguntar qué hacer después de los commits

Este es el punto de DECISIÓN. No hago todo automático, te pregunto qué preferís.

### Las 4 opciones

```
✅ Commits hechos (3 commits)

❓ ¿Qué hacemos ahora?
   [1] Push + PR + Merge a main     (todo el flujo, terminamos)
   [2] Push + PR                    (subo y creo PR, pero no mergeo)
   [3] Solo Push                    (solo subo la rama, sin PR)
   [4] Solo Commits                 (solo commits locales, no subo nada)
```

### Cuándo usar cada una

| Opción | Qué hago | Cuándo usarla |
|--------|----------|---------------|
| **1. Push + PR + Merge** | Push → PR → Merge con squash | Cuando terminaste y querés integrar a main |
| **2. Push + PR** | Push → Crear PR, pero no merge | Cuando querés que alguien revise antes de integrate |
| **3. Solo Push** | Solo `git push` | Cuando todavía vas a seguir trabajando mañana |
| **4. Solo Commits** | Solo `git commit`, nada más | Cuando todavía vas a seguir trabajando HOY |

### Después de tu elección

**Si elegís [1] Push + PR + Merge:**
- Pushear la rama
- Crear el PR
- Hacer squash merge
- Actualizar main local
- Reportar: "✅ Listo, integrado a main"

**Si elegís [2] Push + PR:**
- Pushear la rama
- Crear el PR
- Reportar: "✅ PR creado. Cuando quieras hacer merge, decime."

**Si elegís [3] Solo Push:**
- Pushear la rama
- Reportar: "✅ Push hecho. La rama está en remoto."

**Si elegís [4] Solo Commits:**
- Reportar: "✅ Commits locales listos. Cuando quieras subir, decime."

### En cualquier caso

Si elegís [1], [2] o [3], la próxima vez que ejecutes `/agree` en esa rama, te preguntará si continuás desde ahí o si necesitás algo más.

---

## Paso 9: Hacer el merge a main

Esto solo happens si elegiste [1] "Push + PR + Merge" en el paso anterior.

### El comando

```bash
gh pr merge {numero-pr} --squash --delete-branch --auto
```

### Qué hace cada parte

| flags | Para qué |
|-------|----------|
| `--squash` | Junta todos los commits de la rama en UNO solo |
| `--delete-branch` | Borra la rama del remoto después de merge |
| `--auto` | Acepta el merge automáticamente sin abrir el navegador |

### Por qué squash merge

**Sin squash:**
```
main: A → B → C → D → M1 → M2 → M3
```
Cada commit de la rama queda en main. Historial largo y confuso.

**Con squash:**
```
main: A → B → C → D → M (un commit con todos los cambios)
```
Main queda limpio, con un solo commit que resume todo el cambio.

### Qué pasa después del merge

1. **Verifico que fue exitoso:**
```bash
git fetch --all
git log HEAD..origin/main --oneline  # debería estar vacío
git log origin/main..HEAD --oneline  # debería estar vacío
```

2. **Actualizo main local:**
```bash
git checkout main
git pull origin main
```

3. **Reporte final:**
```
✅ Merge completado!

   Rama: feat/agregar-login-google
   Commits: 3 → 1 (squashed)
   PR: https://github.com/.../pull/123
   Main: actualizado y en sync con remoto
```

### Si hay conflictos

Si Git no puede hacer el merge automáticamente:

```
⚠️ Hay conflictos en el merge.

   Archivos con conflicto:
   - src/auth/service.ts
   - src/routes/auth.ts

   Opciones:
   [1] Resolver los conflictos manualmente (te ayudo)
   [2] Cancelar el merge y dejar la rama para que la revises

¿Qué preferís?
```

Si elegís [1], te ayudo a resolver los conflictos y después continuamos.

---

## Paso 10: Reporte final y resumen

Al terminar (sin importar qué opción elegiste), te doy un resumen de lo que se hizo.

### Si elegiste [1] Push + PR + Merge

```
✅ /agree completado exitosamente!

📦 Integración a main:
   Rama: feat/agregar-login-google
   Commits: 3 (squashed a 1)
   Archivos: 5 modificados
   
🔗 PR: https://github.com/tu-user/tu-repo/pull/123
📍 Merge: verificado y confirmado
📂 Main: actualizado y en sync con remoto

🎯 Listo para el siguiente cambio!
```

### Si elegiste [2] Push + PR

```
✅ PR creado!

📦 Rama pusheada:
   Rama: feat/agregar-login-google
   Commits: 3
   
🔗 PR: https://github.com/tu-user/tu-repo/pull/123

💡 Cuando quieras hacer el merge, ejecutá /agree de nuevo
    y te voy a preguntar si continuamos desde acá.
```

### Si elegiste [3] Solo Push

```
✅ Rama pusheada!

📦 Rama en remoto:
   Rama: feat/agregar-login-google
   Commits: 3
   
🔗 Link: https://github.com/tu-user/tu-repo/tree/feat/agregar-login-google

💡 Para crear el PR más tarde, ejecutá /agree de nuevo.
```

### Si elegiste [4] Solo Commits

```
✅ Commits locales listos!

📦 Commits realizados:
   1. test: agregar tests para login
   2. feat: agregar login con Google
   3. chore: actualizar dependencias

💡 Cuando quieras pushear, ejecutá /agree de nuevo.
```

---

## Manejo de errores

### Error: No hay remoto configurado

```
⚠️ No hay remote configurado.

   git remote -v
   (vacío)

   Agregá el remoto con:
   git remote add origin https://github.com/usuario/repo.git
```

### Error: gh no está instalado

```
⚠️ GitHub CLI (gh) no está instalado.

   El push funciona, pero no puedo crear el PR automáticamente.

   Instalá gh desde: https://cli.github.com/
   O usá: winget install GitHub.cli (en Windows)
   
   Mientras tanto, creá el PR manualmente en:
   https://github.com/{usuario}/{repo}/compare/main...{rama}
```

### Error: Sin permisos para pushear

```
⚠️ No tenés permisos para pushear en este repositorio.

   Verificá:
   - Estás logueado con tu cuenta de GitHub: gh auth status
   - Tenés acceso al repositorio
   - No es un repo de solo lectura
```

### Error: Rama ya existe en el remoto

```
⚠️ La rama {nombre-rama} ya existe en el remoto.

   Opciones:
   [1] Forzar push (peligroso, sobreescribe la rama remota)
   [2] Usar la rama existente (traer cambios del remoto)
   [3] Elegir otro nombre de rama

¿Qué preferís?
```

---

## Resumen del flujo completo

```
┌─────────────────────────────────────────────────────────────┐
│                     FLUJO /agree                             │
├─────────────────────────────────────────────────────────────┤
│  1. Verificar si hay cambios                                 │
│  2. Preguntar qué hizo (descripción)                         │
│  3. Detectar tipo (feat/fix/refactor)                       │
│  4. Crear rama (si es main)                                  │
│  5. Commits atómicos (separados por tipo)                   │
│  6. Preguntar: push? PR? merge? (4 opciones)                │
│  7. Ejecutar lo elegido                                     │
│  8. Reporte final                                           │
└─────────────────────────────────────────────────────────────┘
```

---

## Notas importantes

1. **Nunca trabajo en main directo** — siempre creo rama
2. **Commits atómicos** — separados por tipo, no todo junto
3. **Te pregunto en puntos clave** — no hago todo automático sin confirmar
4. **Squash merge** — main queda con un solo commit limpio
5. **Manejo errores** — te doy opciones claras si algo falla

---

**Este comando está listo para usar.** La próxima vez que ejecutés `/agree`, voy a seguir estas instrucciones paso a paso.