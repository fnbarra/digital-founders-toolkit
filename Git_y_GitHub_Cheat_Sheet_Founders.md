# Git & GitHub Cheat Sheet

Guía de referencia rápida para organizar documentación y seguir el trabajo con Git y GitHub. Está dirigida a personas que colaboran en un producto digital, tengan o no un perfil técnico.

> Git es el sistema de control de versiones; GitHub es una plataforma para alojar repositorios y colaborar sobre ellos. Un commit registra cambios en Git, mientras que un pull request (PR) propone revisarlos e incorporarlos en GitHub.

---

## 1. Estructurar documentación y reportes

Markdown es un formato de texto legible que GitHub muestra con títulos, listas, enlaces y tablas. Usar una estructura común facilita encontrar información y revisar cómo evoluciona.

### Estructura inicial de un repositorio

Adapta la estructura al tamaño y las necesidades del proyecto; evita crear carpetas que todavía no aporten valor:

```text
nombre-proyecto/
├── README.md
├── docs/
│   ├── decisiones/
│   └── reportes/
└── .gitignore
```

- `README.md`: explica qué es el proyecto y cómo orientarse en el repositorio.
- `docs/`: reúne documentación que no conviene mezclar con el código o las tareas del día a día.
- `docs/decisiones/`: registra decisiones relevantes, su contexto y sus consecuencias.
- `docs/reportes/`: conserva reportes periódicos con fecha y alcance claros.
- `.gitignore`: indica qué archivos locales o generados Git no debe seguir.

### Plantilla para el README del proyecto

```markdown
# Nombre del proyecto

## Propósito
Qué problema aborda y para quién.

## Estado
Qué está disponible hoy y qué sigue en curso.

## Cómo empezar
Requisitos e instrucciones para probarlo o colaborar.

## Estructura
Dónde encontrar los componentes y documentos principales.

## Decisiones y contacto
Enlaces a decisiones relevantes, responsables o canales del equipo.
```

### Plantilla de reporte ejecutivo

Guarda cada reporte con fecha en el nombre, por ejemplo `docs/reportes/2026-10-29.md`. Separa los resultados confirmados de los riesgos y los próximos pasos.

```markdown
# Reporte de avance — AAAA-MM-DD

## Resumen
Estado general y cambios relevantes desde el último reporte.

## Avances verificables
- Entregable o cambio: enlace a PR, issue, demo o evidencia.
- Estado: propuesto, en revisión, integrado o desplegado.

## Riesgos y bloqueos
- Riesgo/bloqueo: impacto, responsable y acción siguiente.

## Próximos pasos
- Acción — responsable — fecha objetivo.

## Decisiones necesarias
- Decisión pendiente, opciones y fecha límite.
```

### Buenas prácticas

- Usa títulos descriptivos, fechas y enlaces a la evidencia relacionada.
- Mantén cada documento enfocado en una audiencia y un propósito.
- Especifica el estado con precisión: un PR abierto no equivale a un cambio integrado, y un cambio integrado no prueba por sí solo que esté desplegado.
- Actualiza el documento cuando cambie la información; evita mantener copias contradictorias.

---

## 2. Preparar un repositorio local

Instala Git desde [git-scm.com](https://git-scm.com/) y comprueba la instalación:

```bash
git --version
```

Configura el nombre y el correo que aparecerán en tus commits:

```bash
git config --global user.name "Tu nombre"
git config --global user.email "tu-correo@ejemplo.com"
git config --global --list
```

Inicializa un repositorio nuevo o clona uno existente:

```bash
git init nombre-repo
git clone https://github.com/usuario/nombre-repo.git
```

Al clonar, Git crea una carpeta para el repositorio. Ejecuta los comandos siguientes desde esa carpeta.

## 3. Registrar cambios

```bash
git status                    # Estado de los archivos
git add README.md             # Preparar un archivo para el próximo commit
git add .                     # Preparar todos los cambios del directorio actual
git diff                      # Ver cambios aún no preparados
git diff --staged             # Ver cambios preparados para el commit
git commit -m "docs: actualiza la guía"
git log --oneline             # Historial resumido
```

`git commit -am "mensaje"` prepara y confirma en un solo paso los cambios de archivos que Git ya seguía. No incluye archivos nuevos no rastreados; para esos, usa primero `git add`.

### Deshacer o restaurar con cuidado

```bash
git restore --staged README.md
git restore README.md
git restore --source=<id-commit> -- README.md
```

- `git restore --staged` quita el archivo del área de preparación, pero conserva sus cambios en el directorio de trabajo.
- `git restore` descarta los cambios locales no preparados de ese archivo.
- `--source=<id-commit>` recupera el contenido de ese commit.

**Atención:** restaurar puede sobrescribir cambios locales. Revisa antes `git status` y `git diff`.

Para eliminar un archivo y registrar esa eliminación en el próximo commit:

```bash
git rm nombre-archivo
```

Para inspeccionar un commit anterior sin mover la rama actual:

```bash
git switch --detach <id-commit>
git switch main
```

El modo `--detach` es de solo inspección: los commits que crees ahí no pertenecerán a una rama normal. `main` es un nombre habitual para la rama principal; el repositorio puede usar otro.

---

## 4. Branches y pull requests

Una branch (rama) permite trabajar en un cambio de forma aislada. Un pull request (PR) es una propuesta para revisar e integrar esa rama en otra, normalmente la principal. El PR se crea en GitHub después de subir la rama.

### Flujo habitual

Empieza desde la rama principal actualizada y crea una rama para una tarea:

```bash
git switch main
git pull --ff-only origin main
git switch -c docs/actualiza-guia
```

Haz los cambios y revísalos antes de confirmarlos:

```bash
git status
git diff
git add README.md
git commit -m "docs: actualiza la guía"
```

Publica la rama en GitHub:

```bash
git push -u origin docs/actualiza-guia
```

En GitHub:

1. Abre el repositorio y crea un **New pull request**.
2. Selecciona como **base** la rama que recibirá los cambios (por ejemplo, `main`) y como **compare** tu rama.
3. Escribe un título y una descripción con el propósito, los cambios y cómo verificarlos. Vincula cualquier tarea relacionada.
4. Revisa el diff, solicita revisión y responde a los comentarios. No incluyas cambios que no pertenezcan a la tarea.
5. Cuando se apruebe y pasen las comprobaciones requeridas, integra el PR según las reglas del equipo.

Después de integrar el PR, actualiza tu rama principal local:

```bash
git switch main
git pull --ff-only origin main
```

`--ff-only` evita crear automáticamente un commit de merge si las ramas han divergido. Si el comando falla, revisa el estado y coordina cómo sincronizar la rama; no fuerces el avance sin entender la diferencia.

### Convenciones útiles

- Usa nombres descriptivos para las ramas, por ejemplo `feat/invitaciones` o `fix/error-login`.
- Procura que cada PR resuelva un objetivo acotado y sea fácil de revisar.
- Un commit representa un conjunto coherente de cambios; un PR facilita su revisión e integración.
- El historial aporta trazabilidad, pero no demuestra por sí solo que una funcionalidad esté desplegada o funcione correctamente. Contrasta también revisiones, pruebas y despliegues.

---

## 5. Conectar con GitHub

Si inicializaste el repositorio localmente y creaste un repositorio vacío en GitHub, vincula el remoto:

```bash
git remote add origin https://github.com/usuario/nombre-repo.git
git remote -v
git branch -M main
git push -u origin main
```

`origin` es el nombre convencional del remoto. Si clonaste el repositorio, normalmente ya está configurado.

Para subir commits posteriores de la rama actual y descargar cambios:

```bash
git push
git pull --ff-only origin main
```

Usa `git pull` desde la rama que quieras actualizar y ajusta `main` si el repositorio tiene otro nombre para su rama principal. Antes de sincronizar, revisa `git status` y confirma tus cambios locales.
