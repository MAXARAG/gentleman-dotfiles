# /sync-end — Sincronizar al terminar

Sube las configuraciones y memorias a GitHub. **NO toca el proyecto** — eso lo hacés con `/agree`.

---

## Paso 1: Guardar sesión en Engram

Si no guardaste el resumen de la sesión todavía:

```bash
mem_session_summary
```

Esto guarda lo que trabajaste en esta sesión.

---

## Paso 2: Sync de memories GLOBALES de Engram

```bash
cd ~/engram-sync
engram sync --all
git add .engram/
git diff --cached --stat
```

Si hay cambios, commitear y pushear:

```bash
git commit -m "chore: sync engram memories $(date -u +'%Y-%m-%d')"
git push
```

---

## Paso 3: Sync de dotfiles de OpenCode

```bash
cd ~/.config/opencode
git status
```

Si hay cambios, commitear y pushear:

```bash
git add -A
git commit -m "chore: sync dotfiles $(date -u +'%Y-%m-%d')"
git push
```

---

## Paso 4: Guardar timestamp del último sync

```bash
date -u +"%Y-%m-%dT%H:%M:%SZ" > ~/.config/opencode/last-sync.json
git add last-sync.json
git commit -m "chore: update sync timestamp"
git push
```

---

## Reporte final

```
✅ Sync completado!

   🧠 Engram: memorias subidas
   📁 Dotfiles: subidos
   🕐 Último sync: [timestamp]

💡 El proyecto ya se sincronizó con /agree (opción 1, 2 o 3).
   Cuando vayas a otra PC, ejecutá /sync-start + /agrow.
```

---

## Nota importante

**Este comando NUNCA toca el proyecto.** El proyecto se sincroniza exclusivamente con `/agree`:
- Opción 1: Push + PR + Merge
- Opción 2: Push + PR
- Opción 3: Solo Push

/sync-end solo sube: Engram global y dotfiles de OpenCode.