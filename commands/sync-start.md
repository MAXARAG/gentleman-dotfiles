# /sync-start — Sync al arrancar en una PC nueva

Ejecutá estos pasos al empezar a trabajar en una PC diferente para recuperar todo el contexto.

## Paso 1 — Actualizar dotfiles de OpenCode

```powershell
cd "$env:USERPROFILE\.config\opencode"
git pull
```

Si hay conflictos, resolverlos manualmente. Normally no hay porque solo vos escribís en este repo.

## Paso 2 — Importar memories GLOBALES de Engram

```powershell
cd "$env:USERPROFILE\engram-sync"
git pull
engram sync --import
```

## Paso 3 — Recuperar el proyecto donde estabas trabajando

Buscá en el contexto reciente para saber qué proyecto tenés que continuar:
```powershell
engram context
```

Esto te va a mostrar la última sesión guardada.

## Paso 4 — Preguntar al usuario qué proyecto quiere continuar

Mostrar las sesiones recientes y pedir:

```
🎯 ¿Con qué proyecto querés continuar?
   Última sesión: [nombre del proyecto]

Por favor indicá:
- Link del repositorio: [https://github.com/...]
- Ruta local donde está el proyecto o donde querés que se clone: [ej: D:\Proyectos\MiProyecto]
```

## Paso 5 — Verificar ruta y confirmar antes de operar

Una vez que el usuario proporcione link + ruta:

1. **Si la ruta EXISTE**: Mostrar qué hay ahí y pedir confirmación:
   ```
   📂 La ruta ya existe: [ruta]
   Contenido actual:
   - [lista de archivos/carpetas principales]
   
   ⚠️ ¿Querés hacer `git pull` para actualizar? (s/n)
   ```

2. **Si la ruta NO EXISTE**: Confirmar antes de clonar:
   ```
   🆕 La ruta no existe. Se va a clonar el repo.
   
   ⚠️ ¿Confirmás que querés clonar en: [ruta]? (s/n)
   ```

## Paso 6 — Ejecutar según confirmación

**Si eligió pull (ruta existente):**
```powershell
cd [ruta-local]
git pull
```

**Si eligió clone (ruta nueva):**
```powershell
git clone [link-del-repo] [ruta-local]
```

## Paso 7 — Importar memories del proyecto

Si el proyecto tiene `.engram/`:
```powershell
cd [ruta-local]
git pull
engram sync --import
```

## Paso 8 — Mostrar contexto completo

```powershell
cd [ruta-local]
engram context
```

```
✅ Contexto recuperado.
   Proyecto: [nombre]
   Última sesión: [resumen de mem_context]
   
Podés continuar desde donde dejaste.
```