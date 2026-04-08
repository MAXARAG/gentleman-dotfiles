# /sync-start — Sincronizar al arrancar

Trae las configuraciones y memorias a esta PC. **NO toca el proyecto** — eso lo hacés con `/agrow`.

---

## Paso 1: Actualizar dotfiles de OpenCode

```bash
cd ~/.config/opencode
git pull
```

Si hay conflictos, resolverlos manualmente.

---

## Paso 2: Importar memories GLOBALES de Engram

```bash
cd ~/engram-sync
git pull
engram sync --import
```

Esto trae todas las memorias globales a esta PC.

---

## Paso 3: Guardar timestamp del último sync

```bash
date -u +"%Y-%m-%dT%H:%M:%SZ" > ~/.config/opencode/last-sync.json
```

Esto sirve para saber cuándo fue la última vez que sincronizaste.

---

## Reporte final

```
✅ Sync-start completado!

   📁 Dotfiles: actualizados
   🧠 Engram: memorias importadas
   🕐 Último sync: [timestamp]

💡 Ahora ejecutá /agrow para traer el proyecto.
```

---

## Nota importante

**Este comando NUNCA toca el proyecto.** El proyecto se sincroniza exclusivamente con `/agrow`:
- Trae el proyecto (pull o clone)
- Restaura el estado donde estabas

/sync-start solo baja: Engram global y dotfiles de OpenCode.