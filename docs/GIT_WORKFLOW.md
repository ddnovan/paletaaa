# Guía Estándar de GitHub Flow
<!-- Respuesta de Gemini -->

Esta guía especifica el flujo de trabajo genérico basado en ramas (feature branching), Pull Requests vinculados a Issues y limpieza estricta de entorno.

## 1. Planificación (GitHub Projects)

1. Crear o seleccionar un Issue: Define la tarea, asigna responsable (Assignee) y vincula el issue al proyecto Kanban.

2. Mover estado: Arrastra la tarjeta a la columna In Progress.

3. Identificar ID: Toma nota del número del issue asignado por GitHub (ej. #12).

## 2. Desarrollo Local
### A. Preparar la rama base

Asegúrate de partir de la versión más reciente de la rama principal:

```
git checkout master
git pull origin master
```

### B. Preparar la rama base
Usa un prefijo según el tipo de tarea (feature/, docs/, fix/, refactor/):

```
git checkout -b <tipo>/<nombre-de-la-tarea>
```

### C. Desarrollar y confirmar cambios (Commits)
Añade los archivos modificados al área de preparación y registra commits pequeños y atómicos:

```
# Verificar archivos modificados
git status

# Añadir cambios al staging area
git add <archivo-o-carpeta>

# Registrar el commit con mensaje semántico
git commit -m "<tipo>: <descripción breve en presente/imperativo>"
```

## 3. Publicación y Pull Request (PR)
### A. Subir la rama al remoto
Publica la rama local en GitHub:
```
git push -u origin <tipo>/<nombre-de-la-tarea>
```
### B. Abrir el Pull Request en GitHub

1. Navega al repositorio en el navegador y haz clic en Compare & pull request.

2. Título: Resume de forma clara el cambio implementado.

3. Descripción: Explica el contexto de los cambios e incluye la instrucción de cierre automático:

```
## Descripción
[Resumen de lo que hace este PR]

Closes #<ID_DEL_ISSUE>
```

## 4. Revisión y Fusión (Merge)
1. Revisión: Inspecciona la pestaña Files changed para comprobar que solo se incluyen los cambios deseados.
2. Merge: Haz clic en Merge pull request $\rightarrow$ Confirm merge.
3. Borrado remoto: Haz clic en el botón Delete branch para eliminar la rama del repositorio remoto en GitHub.

## 5. Limpieza y Sincronización Local

Vuelve a la terminal local para dejar el espacio de trabajo totalmente sincronizado y sin ramas huérfanas:

```
# 1. Regresar a la rama principal
git checkout master

# 2. Descargar los cambios fusionados desde la nube
git pull origin master

# 3. Eliminar la rama local ya integrada
git branch -d <tipo>/<nombre-de-la-tarea>

# 4. Limpiar las referencias locales a ramas remotas borradas
git fetch --prune
```