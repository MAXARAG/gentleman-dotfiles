# /revert — Recargar archivo cacheado en VS Code

## Problema

VS Code cachea una versión antigua del archivo en memoria. Cuando lo abrís desde el proyecto:
- La pestaña muestra un `.` blanco en el ícono
- No te deja guardar los cambios
- Muestra menos líneas de las que tiene el archivo en disco

Pero si abrís el archivo desde otra pestaña (fuera del proyecto), aparece completo.

## Solución

1. Abrir la command palette:
   - **Ctrl+Shift+P** (Windows/Linux)
   - **Cmd+Shift+P** (Mac)

2. Buscar y ejecutar:
   ```
   Revert File
   ```

3. El archivo se recargará desde el disco con la versión correcta.

## Alternativa

Si `Revert File` no aparece o no funciona:

```
Ctrl+Shift+P → Developer: Reload Window
```

Esto reinicia VS Code y limpia cualquier cacheo interno.
