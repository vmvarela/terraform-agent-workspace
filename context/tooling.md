# Tooling local — guía genérica de referencia

Referencia para el trabajo del agente en repositorios Terraform clonados bajo `projects/<repo>`. Este workspace no fija versiones ni configuración por defecto; lo que aplique lo define cada repo objetivo.

## Conocimientos base esperados

- `terraform` (o `tofu` si tu organización lo usa), instalado y versionado según documente el repo objetivo (p. ej. vía `mise`/`asdf`/`tfenv` y un `.tool-versions` que ese repo incluya).
- Credenciales del proveedor configuradas fuera del workspace (sesión de la CLI del proveedor, gestor de secretos aprobado o variables de entorno del repo objetivo). Verifica identidad antes de tocar nada (ver `AGENTS.md`).

## Flujo de trabajo sugerido (dentro de un repo objetivo)

1. Usa preferentemente la CLI del proveedor o un gestor de secretos para las credenciales. Si una herramienta requiere `.env`, cárgalo con su mecanismo documentado; **no hagas `source .env`** salvo que sea un fichero shell de confianza que hayas revisado.
2. Comprueba identidad: comando `whoami` / `sts` / equivalente del proveedor (datos no sensibles).
3. `terraform init -input=false` y `terraform plan` — nunca apply directo sin revisión y aprobación.
4. `terraform fmt -recursive` y `terraform validate` antes de commit.
5. **Trata la salida del plan y de `terraform show -json tfplan` como potencialmente sensible**: puede incluir valores sensibles en claro. Úsalo solo si es necesario, y no guardes ni compartas esa salida sin revisar y protegerla.

## Herramientas opcionales

- `tflint`, `checkov`/`tfsec` para lint/seguridad — úsalas si el repo objetivo las configura; este workspace no fija versiones ni configuración por defecto.
- `infracost` u estimador del proveedor para coste (ver skill `terraform-change`).

## Conocimiento del proveedor

Este workspace es neutral. Si tu equipo usa una CLI de proveedor concreta, wrappers internos o comandos de verificación de identidad, documéntalos aquí de forma genérica; los detalles por repo pertenecen a cada repo.
