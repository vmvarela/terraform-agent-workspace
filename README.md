# terraform-agent-workspace

Workspace de operación para agentes sobre infraestructura con Terraform (en español). **Este repositorio no es un proyecto de Terraform**: es la casa del agente — guías, políticas, procedimientos y memoria. El código real se trabaja en repositorios independientes clonados bajo `projects/<repo>`.

## Inicio rápido

1. Ejecuta el agente/cliente **desde la raíz de este workspace**, para que lea `AGENTS.md` y la configuración del workspace (`.opencode/`).
2. Clona cada repositorio objetivo independiente dentro de `projects/`:

```bash
git clone <url-del-repo-objetivo> projects/<nombre>
```

3. Trabaja siempre **dentro del repo objetivo**: lee primero su `AGENTS.md`/`README.md` y verifica allí proveedor, cuenta/entorno, backend y estado (ver `AGENTS.md` de este workspace). Los comandos de Terraform se ejecutan solo dentro del clone seleccionado. **Nunca en la raíz del workspace**: aquí no hay `terraform init` ni plan que hacer.

## Estructura y propósito

| Ruta | Propósito |
| --- | --- |
| `AGENTS.md` | Guía de operación del workspace: contexto mínimo, seguridad, jerarquía de normas y reglas de Terraform para los repos objetivo. |
| `context/` | Referencia genérica editable para el trabajo en los clones (estilo HCL, estado y backends, tooling). Suplementaria: no sustituye las reglas de cada repo. |
| `.agents/skills/` | Procedimientos reutilizables opcionales (p. ej. cambio de Terraform con revisión y aprobación). |
| `memory/` | Libros de registro: errores conocidos y lecciones de DevOps. Sin entradas de partida. |
| `docs/setup.md` | Pasos de configuración del workspace y del agente. |
| `projects/` | Clones de los repos de Terraform con los que trabaja el agente. Vacío de partida (`.gitkeep`). |
| `.opencode/` | Configuración del agente (copiada del workspace local a petición del usuario; ver abajo). |

## Qué contiene `projects/`

Cada clone bajo `projects/<repo>` es un **repositorio Git independiente**, con su propio historial, remoto y reglas. **`projects/*` está excluido por `.gitignore`** en este repo: los clones no forman parte de los commits del workspace ni se empujan con él. Solo se trackea `projects/.gitkeep`.

## Sin credenciales a nivel de workspace

Este repo **no incluye** `.env` ni plantillas de credenciales: las credenciales del proveedor no son apropiadas ni seguras a nivel de workspace. Usa el inicio de sesión de la CLI del proveedor o el gestor de secretos aprobado por tu equipo, siguiendo el flujo documentado por cada repo objetivo. Cualquier `.env*` local queda excluido de Git por `.gitignore`.

## Configuración de OpenCode

La configuración local del agente (`.opencode/`) **ya ha sido copiada a este repo a petición del usuario** (copia exacta). Incluye ajustes personales de modelos y servidores MCP (Atlassian, Playwright) que pueden requerir acceso o adaptación a tu cuenta. `AGENTS.md`, `context/` y `.agents/skills/` definen las reglas de operación legibles por cualquier agente.

## Advertencia importante

Este workspace es **un punto de partida de operación, no una política**. No conocemos (ni inventamos) tu proveedor de nube, organización, backend de estado, cuentas ni variables; todo eso debe documentarlo cada repositorio objetivo. Todo lo que hay aquí son convenciones genéricas y marcadas como tales: revisa y adapta antes de usarlas contra cualquier infraestructura real.
