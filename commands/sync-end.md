# /sync-end — Sync al terminar de trabajar

Ejecutá estos pasos EN ORDEN para dejar todo sincronizado antes de cambiar de PC.

## Paso 1 — Guardar sesión en Engram

Si no lo hiciste todavía, guardá el resumen de la sesión:
- Llamá a `mem_session_summary` con todo lo que trabajaste hoy

## Paso 2 — Leer configuración del proyecto (del último sync-start)

Leer el archivo `~/.config/opencode/sync-project.json` para obtener:
- Repo URL
- Ruta local

Si no existe, preguntar al usuario la ruta del proyecto.

## Paso 3 — Verificar estado actual con fetch

Entrar al directorio del proyecto y hacer fetch para ver qué hay nuevo en el remoto:
```powershell
cd [ruta-del-proyecto]
git fetch --all
```

Mostrar comparación:
```
📂 Proyecto: [nombre]
📁 Ruta: [ruta]
🔗 Repo: [link]

🔍 Estado vs remoto (origin/main):
   - Commits adelante del remoto: [X]
   - Commits detrás del remoto: [Y]
   - Archivos modificados localmente: [lista]
   - Archivos nuevos sin trackear: [lista]
```

## Paso 4 — Mostrar qué se va a pushear y pedir confirmación

Si hay cambios locales para pushear:
```
📦 Cambios pendientes de pushear:
   [lista de archivos cambiados]
   [commits pendientes]

⚠️ ¿Querés hacer commit y push de estos cambios? (s/n)
```

**Si el usuario NO confirma**: Mostrar qué se perdería si no hace push y尊重ar su decisión. No forzar.

**Si el usuario CONFIRMA**: Ir al Paso 5.

## Paso 5 — Ejecutar commit y push (solo si confirmó)

```powershell
cd [ruta-del-proyecto]
git add -A
git status
```

Mostrar qué se va a commitear:
```
📝 Archivos a commitear:
   [lista]

✏️ Ingresá el mensaje del commit (o enter para default):
```

Leer input del usuario para el mensaje, luego:
```powershell
git commit -m "[mensaje-ingresado]"
git push
```

Mostrar resultado:
```
✅ Push exitoso.
   Commit: [hash] - [mensaje]
   Repo: [link]
```

## Paso 6 — Sync de memories del proyecto (después del push)

Si el proyecto tiene `.engram/`:

```powershell
cd [ruta-del-proyecto]
engram sync
git add .engram/
git diff --cached --stat
```

Preguntar:
```
📦 Memories del proyecto con cambios:
   [archivos]

⚠️ ¿Querés commitear y pushear las memories? (s/n)
```

Si confirma:
```powershell
git commit -m "chore: sync engram memories"
git push
```

## Paso 7 — Sync de memories GLOBALES

```powershell
cd "$env:USERPROFILE\engram-sync"
engram sync --all
git add .engram/
git diff --cached --stat
```

Si hay cambios:
```powershell
git commit -m "chore: sync engram $(Get-Date -Format 'yyyy-MM-dd HH:mm')"
git push
```

## Paso 8 — Sync de dotfiles de OpenCode

```powershell
cd "$env:USERPROFILE\.config\opencode"
git add -A
git status
```

Si hay cambios en skills, commands, AGENTS.md, opencode.json, etc:
```powershell
git commit -m "chore: sync opencode dotfiles"
git push
```

## Paso 9 — Confirmar

Mostrar al usuario:
```
✅ Sync completo.

   - Proyecto [nombre]: ✅ Pusheado
   - Repo: [link]
   - Engram global: [con/sin cambios]
   - Dotfiles OpenCode: [con/sin cambios]

Podés continuar desde la otra PC con /sync-start.
```