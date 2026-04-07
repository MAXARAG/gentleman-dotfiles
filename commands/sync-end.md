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

Entrar al directorio del proyecto y hacer fetch para ver el estado real vs remoto:

```powershell
cd [ruta-del-proyecto]
git fetch --all
git status
git diff --stat HEAD origin/main
```

Mostrar comparación detallada:
```
📂 Proyecto: [nombre]
📁 Ruta: [ruta]
🔗 Repo: [link]

🔍 Estado vs remoto (origin/main):
   - Commits locales sin pushear: [X commits]
   - Archivos modificados sin commitear: [lista]
   - Archivos nuevos sin trackear: [lista]
   - Archivos eliminados sin commitear: [lista]
```

**Si no hay nada para pushear**: Avisar y saltar al Paso 6.
```
ℹ️ No hay cambios locales para pushear. El proyecto está en sync con el remoto.
```

## Paso 4 — Mostrar exactamente qué se va a pushear

Antes de pedir confirmación, mostrar con detalle:

```powershell
git diff --stat HEAD
git log origin/main..HEAD --oneline
```

Mostrar:
```
📦 Esto es lo que se va a pushear:

   Commits nuevos:
   - [hash] [mensaje del commit]

   Archivos modificados sin commitear:
   - [archivo] (+X líneas / -Y líneas)

   Archivos nuevos:
   - [archivo]

⚠️ ¿Confirmás que estos cambios son correctos y querés hacer commit y push? (s/n)
```

**Si el usuario NO confirma**: Respetarlo. Mostrar advertencia:
```
⚠️ No se hizo push. Los cambios quedan SOLO en esta PC.
   Si cambiás de PC sin pushear, los perderás.
```

**Si el usuario CONFIRMA**: Ir al Paso 5.

## Paso 5 — Ejecutar commit y push (solo si confirmó)

```powershell
cd [ruta-del-proyecto]
git add -A
```

Mostrar qué se va a commitear y pedir mensaje:
```
📝 Archivos a commitear:
   [lista]

✏️ Ingresá el mensaje del commit (o enter para usar "chore: sync changes"):
```

Leer input del usuario para el mensaje, luego:
```powershell
git commit -m "[mensaje-ingresado]"
git push
```

**Verificar que el push fue exitoso:**
```powershell
git fetch --all
git log HEAD..origin/main --oneline
git log origin/main..HEAD --oneline
```

Si ambos outputs están vacíos, el push fue correcto. Mostrar:
```
✅ Push verificado correctamente.
   - Local y remoto están en sync.
   - Commit: [hash] - [mensaje]
   - Repo: [link]
```

Si hay diferencias → avisar al usuario que algo falló y NO continuar.

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

   - Proyecto [nombre]: ✅ Pusheado y verificado
   - Repo: [link]
   - Engram global: [con/sin cambios]
   - Dotfiles OpenCode: [con/sin cambios]

Podés continuar desde la otra PC con /sync-start.
```
