# Instrucciones para GitHub Copilot (VS Code / Copilot CLI)

`AGENTS.md` en la raíz de este workspace es la guía **canónica** de operación. Léela antes de cualquier tarea y síguela: define contexto mínimo, seguridad, jerarquía de normas y reglas de Terraform.

Reglas esenciales:

1. **Este repo no es un proyecto de Terraform.** Es el espacio de trabajo del agente (guías, políticas, memoria).
2. Para cualquier cambio de código, **selecciona primero el repositorio objetivo** bajo `projects/<repo>` y lee las instrucciones de ese repo (`AGENTS.md`, `README.md`). Esas reglas prevalecen sobre las de este workspace.
3. Ejecuta los comandos de Terraform **solo dentro del repositorio objetivo**. **Nunca en la raíz del workspace** (aquí no hay config de proveedor, backend ni estado).
4. Nunca escribas secretos ni tokens en ficheros, logs, commits o PRs. La autenticación de Atlassian es OAuth por cliente; las credenciales del proveedor viven a nivel usuario/CLI del proveedor.

Detalles de configuración de clientes y servidores MCP: ver `docs/setup.md`.
