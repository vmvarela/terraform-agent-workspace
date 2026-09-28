# Skill: terraform-change

Objetivo: introducir un cambio de infraestructura **en un repositorio objetivo clonado bajo `projects/<repo>`** de forma segura, con revisión humana antes de aplicar. Aplica a cualquier proveedor; personaliza los pasos de verificación según lo que documente ese repo.

## 0. Repositorio objetivo

- Confirma que el trabajo ocurre en un clone bajo `projects/<repo>`, no en la raíz del workspace (la raíz no tiene config de Terraform).
- Lee las instrucciones del propio repo (`AGENTS.md`/`README.md`) y verifica allí proveedor y versión, cuenta/suscripción/proyecto, entorno, región y backend de estado. Esas reglas y su config prevalecen sobre esta guía genérica y sobre `context/terraform/`.

## 1. Contexto y verificación previa

- Comprueba identidad y permisos vigentes con el comando de verificación del proveedor (p. ej. `whoami`, `sts get-caller-identity`, equivalente). Muestra solo datos no sensibles.
- Ejecuta `terraform init -input=false` (o `-upgrade` si cambian versiones de módulos/providers) **dentro del repo objetivo**.

## 2. Refrescar el estado real

- Ejecuta `terraform plan -refresh-only` para detectar drift antes de proponer cambios. Este comando consulta el proveedor y el estado remoto; si el refresh falla, informa la limitación. **`-refresh=false` no sirve para detectar drift.**
- Si hay drift, preséntalo y decide con el usuario si se reconcilia o es esperado.

## 3. Plan

- Genera un plan guardado: `terraform plan -out=tfplan` (sin `-auto-approve`).
- Resume claramente: recursos a `create / update / replace / destroy`, y atributos clave que cambian.
- **Nunca afirmes que un plan es inofensivo.** Un `replace` puede implicar downtime o pérdida de datos; revisa las `forces replacement` línea a línea.
- **Un plan guardado puede contener valores sensibles en claro.** No lo commitees, no lo compartas sin protección y trátalo como dato confidencial (ver `context/tooling.md`).

## 4. Impacto y coste

- Estima coste si la herramienta que use ese repo está disponible (p. ej. `infracost`, estimador del proveedor).
- Evalúa impacto en servicio: recursos con `replace`, dependencias downstream, ventana de mantenimiento.
- Revisa permisos/IAM necesarios para la operación.

## 5. Revisión y aprobación humana

- Presenta al usuario: resumen del plan, riesgos, coste estimado y rollback previsto.
- **`terraform apply` requiere aprobación humana explícita** en el repo objetivo (vía PR, ticket o registro que use ese repo). El agente no aplica por decisión propia ni por timeout silencioso.
- Registra quién aprobó y cuándo.

## 6. Apply y verificación posterior

- Aplica con `terraform apply tfplan` (contra el plan ya revisado, no un nuevo plan implícito).
- Verifica outputs y estado: `terraform output`, comprobaciones del proveedor de que el recurso existe y está sano.
- Si el apply falla: no reintentes en bucle; lee el error, consulta `memory/known-errors.md` y propón corrección.

## Prohibiciones

- Sin solicitud explícita del usuario y su aprobación, nunca `destroy` ni `-destroy`.
- Sin editar el estado a mano sin plan de migración revisado.
- Sin secretos en plan files commiteados ni en logs.
- Sin ejecutar comandos de Terraform fuera del repo objetivo (nunca en la raíz del workspace).
