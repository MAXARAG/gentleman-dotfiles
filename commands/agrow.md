---
description: Clonar o pull un proyecto desde GitHub (clone | pull en la ruta que elijas)
agent: gentleman
---

# /agrow — Clonar o Actualizar un Proyecto

## Qué hace este comando

Trae un proyecto de GitHub a tu PC. Si ya existe, hace pull (trae los últimos cambios). Si no existe, lo clona.

**Problema que resuelve:**
- ¿Tenés que buscar la URL del repo cada vez que empezás un proyecto?
- ¿Te olvidás de hacer pull antes de empezar a trabajar?
- ¿No sabés si el proyecto ya está en tu PC o hay que clonarlo?

**Solución:** Un solo comando que detecta si existe o no y hace lo correcto automáticamente.

---

## Paso 1: Pedir la URL del repo y la ruta local

### Por qué es importante

Necesito saber dos cosas:
1. **URL del repo** → de dónde clonar
2. **Ruta local** → dónde guardarlo en tu PC

Sin eso, no puedo hacer nada.

### Lo que yo te pregunto

```
📦 Nuevo proyecto

❓ ¿Cuál es la URL del repo?
   (ej: https://github.com/usuario/mi-app)
```

**Vos:** "https://github.com/usuario/mi-app"

```
❓ ¿En qué ruta querés guardar el proyecto?
   (ej: D:/Proyectos/mi-app o ~/proyectos/mi-app)
```

**Vos:** "D:/Proyectos/mi-app"

---

### Por qué dos preguntas

- La **URL** la necesitás para el `git clone` (o para verificar el remoto)
- La **ruta** te permite elegir dónde tener el proyecto (cada uno tiene su propia organización)

---

## Paso 2: Verificar si el proyecto ya existe

Después de que me das la URL y la ruta, verifico si ya existe:

```bash
# Verificar si la carpeta existe
if [ -d "D:/Proyectos/mi-app" ]; then
  echo "EXISTE"
else
  echo "NO EXISTE"
fi
```

| Resultado | Qué hago |
|-----------|----------|
| Ya existe | Hacer pull (traer cambios) |
| No existe | Clonar el repo |

---

## Paso 3: Clonar o Pull

### Si la carpeta NO existe → Clonar

```bash
git clone {url-repo} {ruta-local}
```

**Ejemplo:**
```bash
git clone https://github.com/usuario/mi-app D:/Proyectos/mi-app
```

**Qué hace:**
- Descarga todo el repo a la carpeta que especificaste
- Configura el remoto `origin` automáticamente
- Trae la rama por defecto (main o master)

**Qué te muestro después:**
```
✅ Clonado exitosamente!

   Repo: https://github.com/usuario/mi-app
   Ruta: D:/Proyectos/mi-app
   Ramas: main, develop
```

---

### Si la carpeta YA existe → Hacer Pull

```bash
cd {ruta-local}
git fetch --all
```

**Qué hace:**
- Trae TODAS las ramas del remoto (no solo main)
- Actualiza las referencias a ramas remotas

**Después te pregunto:**

```
✅ Fetch completado! ramas disponibles:
   - main
   - develop  
   - feat/trabajo-en-continuidad

❓ ¿En qué rama querés trabajar?
   [1] main
   [2] feat/trabajo-en-continuidad
   [3] Otrarama (especificá cuál)
```

---

### Si elegís [2] - La rama que estabas usando

**Vos:** "2" (o decís el nombre de la rama)

**Yo:**
```bash
git checkout feat/trabajo-en-continuidad
```

Pero si la rama NO existe todavía en local:

```bash
git checkout -b feat/trabajo-en-continuidad origin/feat/trabajo-en-continuidad
```

Qué hace cada comando:

| Comando | Cuándo usarla |
|---------|---------------|
| `git checkout feat/rama` | Cuando la rama ya existe en local |
| `git checkout -b feat/rama origin/feat/rama` | Cuando la rama solo está en el remoto |

**Reporte:**
```
✅ Cambiado a la rama: feat/trabajo-en-continuidad

   📍 Rama actual: feat/trabajo-en-continuidad
   📝 Ultimos commits en esta rama:
   - abc1234 agregué login
   - def5678 arreglé bug

📝 Listo para continuar trabajando! Ejecutá /agree cuando terminés.
```

**Antes de hacer pull, verifico:**
```bash
git status
```

Si tenés cambios sin commitear, te pregunto:

```
⚠️ Tenés cambios sin commitear en esta carpeta.

   git status
   modified: src/auth.ts
   modified: tests/auth.test.ts

   Opciones:
   [1] Hacer stash (guardar cambios temporalmente) y después hacer pull
   [2] Cancelar el pull (dejar los cambios como están)

¿Qué preferís?
```

---

### Después del pull (ya exista o no)

Verifico el estado final:

```bash
git log --oneline -3
git branch --show-current
```

**Reporte final:**
```
✅ Proyecto actualizado!

   Repo: https://github.com/usuario/mi-app
   Ruta: D:/Proyectos/mi-app
   Rama actual: main
   Últimos commits:
   - abc1234 commit más reciente
   - def5678 commit anterior
   - ghi9012 commit anterior

📝 Listo para trabajar! Ejecutá /agree cuando terminés.
```