# AGENTS.md

Guía de operación para agentes que trabajan en **este workspace de infraestructura**. Este repo NO es un proyecto de Terraform: es un espacio de trabajo del agente (documentación, políticas y procedimientos). El código de Terraform vive en repositorios independientes clonados dentro de `projects/<repo>`.

## Qué es este workspace (y qué no)

- **Este repo** contiene: guías para el agente (`AGENTS.md`), referencia genérica (`context/`), procedimientos opcionales (`.agents/skills/`), registros de aprendizaje (`memory/`) y documentación operativa (`docs/`).
- **Este repo NO contiene**: código Terraform, configuración de proveedor, backend ni estado.
- **El código real** está en los clones bajo `projects/<repo>`. Cada clone es un repositorio Git independiente con sus propias reglas, su `.gitignore`, su CI y su propia configuración de proveedor/backend.

## Regla fundamental para cualquier cambio de código

1. **Identifica el repositorio objetivo** bajo `projects/<repo>` donde vive el cambio. Si no hay clone local, pídelo o clónalo antes de tocar nada.
2. **Lee las instrucciones del propio repo** (`AGENTS.md`, `README.md`, `CONTRIBUTING.md`, políticas internas si existen). Esas reglas tienen prioridad sobre este documento.
3. **Verifica allí, no aquí**: proveedor y versión, cuenta/suscripción/proyecto, entorno, región, backend de estado, workspace y configuración de CI. No asumas valores: confírmalos en la config y documentación del repo objetivo.
4. Ejecuta los comandos de Terraform **solo dentro del repositorio objetivo**. Nunca en la raíz del workspace: la raíz no tiene config de Terraform ni debería tenerla.

## Principios de trabajo

- **Contexto mínimo**: empieza por la tarea y los archivos del proyecto objetivo. No precargues directorios completos. Consulta `context/` solo cuando la tarea toque ese tema.
- **Verifica antes de cambiar**: confirma proveedor, cuenta/entorno, backend de estado y región en el repo objetivo antes de proponer o ejecutar cambios. No asumas valores.
- **Seguridad primero**: nunca escribas secretos en ficheros, logs, commits, PRs o respuestas. Reporta hallazgos por ruta y nombre de clave, no por valor. Usa el inicio de sesión de la CLI del proveedor o el gestor de secretos aprobado.
- **Cambia lo mínimo**: ediciones quirúrgicas en el repo objetivo, sin refactorizaciones no solicitadas.

## Jerarquía de normas

Las guías de `context/terraform/` y los skills son **referencia genérica complementaria** para el trabajo en los clones. **Nunca prevalecen** sobre: las reglas del repo objetivo, las políticas de la organización ni la configuración real de versión de proveedor/backend/estado de ese repo. Si hay conflicto, manda el repo objetivo; si es ambiguo, pregunta.

## Índice tarea → contexto / skill

| Tarea | Leer / usar |
| --- | --- |
| Arrancar o configurar el workspace | `README.md`, `docs/setup.md` |
| Configurar el cliente/MCP (OpenCode, Codex, Copilot, Claude Code) | `docs/setup.md` (§ Configuración de clientes y MCP) |
| Escribir o revisar HCL (en un repo objetivo) | `context/terraform/style.md` |
| Estado, backend, imports, migraciones (en un repo objetivo) | `context/terraform/state-and-backends.md` |
| Herramientas locales (CLI, versiones) | `context/tooling.md` |
| Plan → revisión → aprobación → apply (repo objetivo) | `.agents/skills/terraform-change/SKILL.md` |
| Errores recurrentes | `memory/known-errors.md` |
| Lecciones de DevOps | `memory/devops-lessons.md` |

## Reglas de Terraform (aplican dentro de los repos objetivo)

1. Ejecuta `terraform init -input=false` en el repo objetivo antes de plan si hay módulos nuevos o cambio de versión.
2. Genera siempre un plan y preséntalo (resumen de recursos a crear/cambiar/destruir) antes de cualquier `apply`.
3. **`terraform apply` requiere aprobación humana explícita y registrada.** Los agentes no aplican por su cuenta.
4. **Nunca ejecutes `terraform destroy` ni uses `-destroy` sin una solicitud explícita del usuario y su aprobación.** Se piden ambos: la petición y la aprobación son pasos separados.
5. No deshabilites el bloqueo del estado ni compruebes credenciales imprimiendo secretos; valida la identidad con el comando recomendado por el proveedor, mostrando solo datos no sensibles.
6. No edites el estado (`terraform state mv`, `terraform state rm`, `terraform import`) sin un plan de migración revisado por el usuario. Las migraciones de estado entre backends requieren precaución extra: detén la operación ante cualquier duda y consulta al usuario.

## Memoria y aprendizaje

- Durante la tarea, anota correcciones del usuario y comportamientos no obvios verificados en el fichero de memoria correspondiente (`memory/`). Busca una entrada existente antes de crear una.
- `memory/` registra lecciones; `context/` define convenciones genéricas. No mezcles.
