# Setup — configuración del workspace y del agente

Este workspace **no es un proyecto de Terraform**: no tiene proveedor, backend, credenciales ni código `.tf`, y no debe tenerlos. El código real vive en repositorios independientes clonados bajo `projects/<repo>`. Estos pasos solo preparan el workspace y el agente.

## 1. Requisitos

- El agente/cliente que uses (p. ej. OpenCode), ejecutado **desde la raíz de este workspace** para que lea `AGENTS.md` y la configuración del workspace (`.opencode/`).
- En cada repo objetivo que clones: `terraform` (o `tofu`) instalado localmente. Gestiona la versión con `mise`/`asdf`/`tfenv` según lo que documente ese repo.
- Credenciales del proveedor de nube disponibles a nivel usuario (CLI del proveedor con sesión iniciada, o gestor de secretos aprobado por tu equipo). **No a nivel de workspace**: este repo no incluye ni `.env` ni plantillas de credenciales a propósito.

## 2. Clonar los repositorios objetivo

Clona cada repositorio independiente dentro de `projects/`:

```bash
git clone <url-del-repo-objetivo> projects/<nombre>
```

- Cada clone es un repositorio Git independiente: `projects/*` está excluido por el `.gitignore` de este workspace (solo se trackea `projects/.gitkeep`), así que los clones no entran en los commits del workspace.
- Antes de trabajar en un clone: lee **su** `AGENTS.md`/`README.md` y verifica allí proveedor y versión, cuenta/suscripción/proyecto, entorno, región, backend de estado y CI. Ver `AGENTS.md` de este workspace.

## 3. Instalar y ejecutar el agente

Desde este workspace, si usas OpenCode:

```bash
npm i -g opencode-ai  # o el instalador documentado por tu equipo
opencode              # ejecutado en la raíz del workspace
```

Al arrancar desde la raíz, el agente lee `AGENTS.md` (reglas de operación) y la configuración del workspace.

## 4. Configuración de clientes y servidores MCP

Multi-cliente: los mismos servidores MCP están disponibles vía archivos portables, sin credenciales en el repo.

| Cliente | Configuración usada | Notas |
| --- | --- | --- |
| OpenCode | `.opencode/opencode.json` (ya presente) | Copia exacta a petición del usuario; puede requerir adaptación a tu cuenta. No modificar aquí. |
| Codex (CLI/IDE) | `.codex/config.toml` | El project config solo se carga cuando el workspace está marcado como **trusted**. Atlassian OAuth autorizable por cliente. |
| GitHub Copilot (VS Code / Copilot CLI) | `.mcp.json` + `.github/copilot-instructions.md` | `.mcp.json` portable (formato `mcpServers`). VS Code Local Agent puede requerir `chat.useAgentsMdFile` para descubrir `AGENTS.md` de forma nativa; las instrucciones de Copilot apuntan a la guía canónica (`AGENTS.md`). |
| Claude Code | `.mcp.json` + `CLAUDE.md` | `CLAUDE.md` solo importa `@AGENTS.md`. Aprobación de servidores y OAuth de Atlassian son por cliente. |

Servidores MCP configurados (en `.mcp.json` y `.codex/config.toml`):

- **Atlassian** — remoto: `https://mcp.atlassian.com/v2/mcp` (formato `http`). Sin credenciales ni tokens en el repo: el OAuth es por cliente y se autoriza al primer uso en cada uno.
- **Playwright** — local por `npx` con `@playwright/mcp@latest` y `--caps=core,network,vision`. En el `.mcp.json` compartido se omite `type` explícito: `command`/`args` denotan stdio, lo que da máxima portabilidad (Claude, VS Code Agent Host, Copilot CLI). El tag `@latest` es mutable; puede fijarse a una versión concreta si el propietario necesita estabilidad.

Notas de alcance:

- **`.vscode/mcp.json` no es necesario** y no existe: el `.mcp.json` portable es el formato con soporte nativo en VS Code. Tampoco hay `.github/mcp.json` ni configuración en el home del usuario.
- **Copilot Cloud Agent no está configurado aquí**: usa ajustes del repositorio en GitHub (no archivos locales) y no soporta el OAuth remoto de Atlassian directamente.
- **Credenciales**: nunca en ficheros del repo (tokens OAuth, `clientId`, headers, `.env` de workspace). Atlassian se autoriza vía OAuth por cliente y las credenciales del proveedor de nube son a nivel usuario (ver §5).

## 5. Credenciales

Usa preferentemente el inicio de sesión de la CLI del proveedor o el gestor de secretos aprobado por tu equipo, según el flujo documentado por cada repo objetivo. Si una herramienta concreta requiere `.env`, crea ese fichero local dentro del repo objetivo (está excluido de Git) y cárgalo con el mecanismo documentado para esa herramienta. **No hagas `source .env`** salvo que sea un fichero shell de confianza que hayas revisado.

## 6. Trabajo diario (resumen)

1. Arranca el agente en la raíz del workspace.
2. Selecciona el repo objetivo bajo `projects/<repo>` y lee sus instrucciones.
3. Verifica allí proveedor, cuenta/entorno, backend y estado.
4. Ejecuta los comandos de Terraform **solo dentro del clone** (`init`, `plan`, etc.), nunca en la raíz del workspace: aquí no hay config de Terraform que inicializar.
5. Sigue el skill `terraform-change` para cualquier cambio: plan → revisión → **aprobación humana explícita** → apply.
