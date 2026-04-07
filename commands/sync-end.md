# /sync-end — Sync al terminar de trabajar

Ejecutá estos pasos EN ORDEN para dejar todo sincronizado antes de cambiar de PC.

## Paso 1 — Guardar sesión en Engram

Si no lo hiciste todavía, guardá el resumen de la sesión:
- Llamá a `mem_session_summary` con todo lo que trabajaste hoy

## Paso 2 — Verificar que haya una ruta de proyecto registrada

Antes de hacer cualquier cosa, verificar si hay una ruta de proyecto guardada (del último `/sync-start`):
- Si hay una ruta guardada, usarla como referencia
- Si no hay, preguntar al usuario la ruta del proyecto

## Paso 3 — Sync de memories del proyecto actual

En el directorio del proyecto donde estabas trabajando:

```powershell
cd [ruta-del-proyecto]
engram sync
git add .engram/
git diff --cached --stat
```

Si hay cambios, preguntar al usuario si quiere commitear y pushear:
```
📦 Changes in .engram/ del proyecto:
   [lista de archivos cambiados]

⚠️ ¿Querés commitear y pushear las memories del proyecto? (s/n)
```

Si el usuario confirma:
```powershell
git commit -m "chore: sync engram memories"
git push
```

Si no hay `.engram/` en el proyecto (proyecto sin sync configurado), saltá este paso.

## Paso 4 — Confirmar antes de hacer push del proyecto

**Antes de hacer cualquier cosa**, preguntar al usuario:

```
🔍 Estado actual del proyecto en [ruta]:
   git status
```

Mostrar el status y luego preguntar:
```
⚠️ ¿Querés hacer commit y push de los cambios del proyecto? (s/n)

   - Archivos modificados: [lista]
   - Archivos nuevos: [lista]
```

**Solo si el usuario confirma**, ejecutar:
```powershell
cd [ruta-del-proyecto]
git add -A
git status
```

Luego mostrar qué se va a commitear y pedir confirmación final:
```
📝 Commiteando:
   [lista de archivos]

⚠️ ¿Confirmás el commit? (s/n)
```

Si confirma:
```powershell
git commit -m "[tipo]: [descripción]"
git push
```

## Paso 5 — Recordar link del repositorio para futura referencia

Guardar en la memoria (no en archivo) que este proyecto usa:
- Repo: [link]
- Ruta: [ruta-local]

Esto se usa automáticamente en el próximo `/sync-start`.

## Paso 6 — Sync de memories GLOBALES

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

## Paso 7 — Sync de dotfiles de OpenCode

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

## Paso 8 — Confirmar

Mostrar al usuario:
```
✅ Sync completo. Todo pusheado.
   - Proyecto [nombre]: [con/sin cambios]
   - Engram global: [con/sin cambios]
   - Dotfiles OpenCode: [con/sin cambios]

Podés continuar desde la otra PC con /sync-start.
```