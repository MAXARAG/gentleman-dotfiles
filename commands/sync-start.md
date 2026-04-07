# /sync-start — Sync al arrancar en una PC nueva

Ejecutá estos pasos al empezar a trabajar en una PC diferente para recuperar todo el contexto.

## Paso 1 — Actualizar dotfiles de OpenCode

```powershell
cd "$env:USERPROFILE\.config\opencode"
git pull
```

Si hay conflictos, resolverlos manualmente. Normalmente no hay porque solo vos escribís en este repo.

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
- Ruta local donde está el proyecto: [ej: D:\Proyectos\MiProyecto]
```

Una vez que el usuario proporcione estos datos:

1. Si la ruta NO existe, clonar el repositorio:
   ```powershell
   git clone [link-del-repo] [ruta-local]
   ```

2. Si la ruta ya existe, hacer pull:
   ```powershell
   cd [ruta-local]
   git pull
   ```

3. Importar memories del proyecto (si tiene `.engram/`):
   ```powershell
   cd [ruta-local]
   git pull
   engram sync --import
   ```

4. Mostrar contexto del proyecto:
   ```powershell
   cd [ruta-local]
   engram context
   ```

## Paso 5 — Mostrar contexto completo

```
✅ Contexto recuperado.
   Proyecto: [nombre]
   Última sesión: [resumen de mem_context]
   
Podés continuar desde donde dejaste.
```