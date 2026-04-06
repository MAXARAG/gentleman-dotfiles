# Setup OpenCode en PC nueva (notebook)

> **Para la IA**: Este archivo es el guión de instalación. Leelo completo antes de ejecutar cualquier comando. Ejecutá cada paso en orden. Si algo falla, detenete y reportalo.

---

## Contexto

Este es el entorno de trabajo de un desarrollador que usa **OpenCode** como CLI principal con:
- **Gentleman AI** como persona (mentor senior, Rioplatense Spanish)
- **Engram** para memoria persistente entre sesiones
- **SDD** (Spec-Driven Development) para cambios significativos
- Sync entre PC de escritorio (`C:\Users\PC`) y notebook (`C:\Users\Juan`)

---

## Requisitos previos

Verificá que tenés instalado:

```powershell
# Git
git --version

# Go 1.24+ (necesario para engram)
go version

# OpenCode
opencode --version

# Node.js (para plugins)
node --version
```

Si falta algo:
- **Git**: https://git-scm.com/download/win
- **Go**: https://go.dev/dl/
- **OpenCode**: `winget install opencode` o https://opencode.ai
- **Node.js**: https://nodejs.org (LTS)

---

## Paso 1 — Limpiar configuración anterior (Claude Code + VS Code)

> ⚠️ Este paso elimina la config vieja de Gentle AI para Claude Code y VS Code. Las **credenciales de Claude Code se conservan** para que siga funcionando desde OpenCode.

### 1a — Limpiar Claude Code config (conservar credenciales)

```powershell
# Ver qué hay en ~/.claude/
ls "$env:USERPROFILE\.claude"

# Eliminar config vieja (skills, CLAUDE.md, settings, commands, mcp)
# CONSERVAR: .credentials.json (auth de Claude Code)
$claudeDir = "$env:USERPROFILE\.claude"
$keepFiles = @(".credentials.json")

Get-ChildItem $claudeDir | Where-Object {
    $_.Name -notin $keepFiles
} | Remove-Item -Recurse -Force

Write-Host "Claude Code config limpiada. Credenciales conservadas."
```

### 1b — Limpiar config de VS Code Gentle AI (opcional)

Los archivos de Copilot/GitHub auth están en el sistema (Windows Credential Manager), no en archivos. Lo que podés limpiar son los archivos de instrucciones del proyecto:

```powershell
# Si querés revisar qué archivos de copilot-instructions hay en tus proyectos:
# Esto es solo informativo - no borres nada automáticamente
Get-ChildItem -Path "$env:USERPROFILE\Proyectos" -Recurse -Filter "copilot-instructions.md" 2>$null
```

---

## Paso 2 — Instalar Engram

```powershell
# Opción A: via Go install (recomendado)
go install github.com/Gentleman-Programming/engram/cmd/engram@latest

# Verificar
engram version
```

Si `engram` no está en el PATH después de instalar:
```powershell
# Agregar Go bin al PATH permanentemente
$goBin = "$env:USERPROFILE\go\bin"
[Environment]::SetEnvironmentVariable("PATH", $env:PATH + ";$goBin", "User")
# Reiniciar PowerShell después de esto
```

---

## Paso 3 — Clonar dotfiles de OpenCode

```powershell
# Si ~/.config/opencode/ no existe todavía
New-Item -ItemType Directory -Force -Path "$env:USERPROFILE\.config\opencode"

# Si ya existe con config vieja, hacer backup primero
# Move-Item "$env:USERPROFILE\.config\opencode" "$env:USERPROFILE\.config\opencode.backup"

# Clonar los dotfiles
git clone https://github.com/MAXARAG/gentleman-dotfiles.git "$env:USERPROFILE\.config\opencode"
```

> Si `~/.config/opencode/` ya existe con contenido, alternativa:
> ```powershell
> cd "$env:USERPROFILE\.config\opencode"
> git init
> git remote add origin https://github.com/MAXARAG/gentleman-dotfiles.git
> git fetch origin
> git reset --hard origin/main
> ```

---

## Paso 4 — Instalar dependencias de plugins

```powershell
cd "$env:USERPROFILE\.config\opencode"
npm install
```

Esto instala `@opencode-ai/plugin` y `unique-names-generator` para el plugin de Engram y background-agents.

---

## Paso 5 — Configurar Engram en OpenCode

```powershell
# Registrar Engram como MCP en OpenCode
engram setup opencode
```

> **Nota**: El `opencode.json` ya viene configurado en los dotfiles con `"command": ["engram", "mcp", "--tools=agent"]`. Si `engram setup opencode` sobreescribe algo, verificá que el `opencode.json` del repo siga siendo el correcto.

---

## Paso 6 — Clonar engram-memories para sync global

```powershell
# Clonar el repo de sync global de Engram
git clone https://github.com/MAXARAG/engram-memories.git "$env:USERPROFILE\engram-sync"
```

---

## Paso 7 — Importar memories

```powershell
# Importar memories globales
cd "$env:USERPROFILE\engram-sync"
engram sync --import

# Ver contexto para saber en qué estabas trabajando
engram context
```

---

## Paso 8 — Clonar el proyecto donde estabas trabajando

Engram context te mostrará el último proyecto activo. Cloná ese proyecto:

```powershell
# Ejemplo:
# git clone <url-del-proyecto> "$env:USERPROFILE\Proyectos\<nombre>"
# cd "$env:USERPROFILE\Proyectos\<nombre>"
```

Una vez dentro del proyecto, si tiene `.engram/manifest.json`, el plugin de OpenCode importa las memories automáticamente al abrir la sesión.

---

## Paso 9 — Verificar que todo funciona

Abrí OpenCode en el directorio del proyecto:

```powershell
cd <directorio-del-proyecto>
opencode
```

Dentro de OpenCode:
1. Escribí `/sync-start` para recuperar el contexto completo
2. Verificá que Engram responde (debería cargarse como MCP tool)
3. Escribí `engram context` en una sesión para ver la última memoria

---

## Flujo diario

### Al terminar de trabajar (en cualquier PC)
```
/sync-end
```

### Al arrancar en la otra PC
```
/sync-start
```

---

## Troubleshooting

### "engram: command not found"
```powershell
# Verificar que go/bin está en PATH
echo $env:PATH | Select-String "go"
# Si no está: agregar manualmente (ver Paso 2)
```

### "opencode.json tiene rutas de C:\Users\PC\"
El `opencode.json` usa rutas relativas (`./prompts/sdd/`). Si ves rutas absolutas con otro usuario, algo salió mal en el clone. Revisá `git log` y `git diff`.

### Plugin de Engram no carga
```powershell
cd "$env:USERPROFILE\.config\opencode"
npm install
# Verificar que node_modules/ existe
```

### Memories no se importan
```powershell
cd "$env:USERPROFILE\engram-sync"
git pull
engram sync --status
engram sync --import
```
