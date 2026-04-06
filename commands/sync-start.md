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

Esto te va a mostrar la última sesión guardada. Abrí el proyecto correspondiente.

## Paso 4 — Importar memories del proyecto

Una vez dentro del directorio del proyecto, si tiene `.engram/`:
```powershell
git pull
engram sync --import
```

El plugin de OpenCode también hace esto automáticamente al detectar `.engram/manifest.json`.

## Paso 5 — Mostrar contexto completo

```powershell
engram context
```

Mostrá al usuario el resumen de la última sesión para que sepa exactamente dónde estaba y qué falta.

```
✅ Contexto recuperado.
   Última sesión: [resumen de mem_context]
   
Podés continuar desde donde dejaste.
```
