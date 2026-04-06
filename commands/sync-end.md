# /sync-end — Sync al terminar de trabajar

Ejecutá estos pasos EN ORDEN para dejar todo sincronizado antes de cambiar de PC.

## Paso 1 — Guardar sesión en Engram

Si no lo hiciste todavía, guardá el resumen de la sesión:
- Llamá a `mem_session_summary` con todo lo que trabajaste hoy

## Paso 2 — Sync de memories del proyecto actual

En el directorio del proyecto donde estabas trabajando:

```powershell
engram sync
git add .engram/
git diff --cached --stat
```

Si hay cambios, commiteá y pusheá:
```powershell
git commit -m "chore: sync engram memories"
git push
```

Si no hay `.engram/` en el proyecto (proyecto sin sync configurado), saltá este paso.

## Paso 3 — Sync de memories GLOBALES

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

## Paso 4 — Sync de dotfiles de OpenCode

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

## Paso 5 — Confirmar

Mostrá al usuario:
```
✅ Sync completo. Todo pusheado.
   - Engram del proyecto: [con/sin cambios]
   - Engram global: [con/sin cambios]
   - Dotfiles OpenCode: [con/sin cambios]

Podés continuar desde la notebook con /sync-start.
```
