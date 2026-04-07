# /sync-end — Sync al terminar de trabajar

Ejecutá estos pasos EN ORDEN para dejar todo sincronizado antes de cambiar de PC.

## Paso 1 — Guardar sesión en Engram

Si no lo hiciste todavía, guardá el resumen de la sesión:
- Llamá a `mem_session_summary` con todo lo que trabajaste hoy

## Paso 2 — Sync de memories del proyecto actual

En el directorio del proyecto donde estabas trabajando:

```powershell
cd [ruta-del-proyecto]
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

## Paso 3 — Push del proyecto al repositorio

Hacer push de todos los cambios del proyecto al repositorio remoto:

```powershell
cd [ruta-del-proyecto]
git add -A
git status
```

Si hay cambios que querés pushear:
```powershell
git commit -m "[tipo]: [descripción]"
git push
```

**IMPORTANTE**: Pedir al usuario confirmación antes de pushear,告知 qué archivos cambiaron.

## Paso 4 — Sync de memories GLOBALES

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

## Paso 5 — Sync de dotfiles de OpenCode

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

## Paso 6 — Confirmar

Mostrar al usuario:
```
✅ Sync completo. Todo pusheado.
   - Proyecto [nombre]: [con/sin cambios]
   - Engram global: [con/sin cambios]
   - Dotfiles OpenCode: [con/sin cambios]

Podés continuar desde la otra PC con /sync-start.
```