# Estilo HCL — convenciones genéricas de referencia

Convenciones genéricas y mínimas para revisar o escribir HCL **en repositorios Terraform clonados bajo `projects/<repo>`**. Son un punto de partida editable, no una política: tienen carácter suplementario y **nunca prevalecen sobre las reglas del repo objetivo ni sobre las convenciones de tu organización**. Si ese repo define su propio estilo (lint config, `.tflint.hcl`, guía en su README), manda lo suyo.

## Formato

- `terraform fmt` en el repo objetivo antes de commit; su CI puede reforzarlo con `terraform fmt -check`.
- 2 espacios, sin tabs; comillas dobles; sin finales de línea en blanco innecesarios.
- Nombres de recursos y variables en `snake_case`, descriptivos y sin prefijos redundantes con el tipo (`aws_subnet.public` mejor que `aws_subnet.aws_public_subnet` — salvo que la guía del repo diga lo contrario).

## Estructura

- Un módulo = un propósito. Interfaces de módulos via `variables.tf` / `outputs.tf`; implementación en `main.tf` (o ficheros temáticos) junto a `versions.tf`.
- `versions.tf` con `required_version`, `required_providers` y backend definido por el repo objetivo.
- Pin de proveedores en `required_providers` con `~>` en minor para predecibilidad; el lockfile (`.terraform.lock.hcl`) suele versionarse en el repo objetivo.

## Prácticas

- Prioriza `for_each` frente a `count` para colecciones con identidad estable.
- Valida entradas con `validation` blocks en variables en lugar de checks ad-hoc.
- No introduzcas `local-exec`/`remote-exec` salvo necesidad justificada y revisada.
- Marca `sensitive = true` en outputs/variables con datos confidenciales; aun así, no los imprimas en logs.

## Lo que esta guía NO asume

Este workspace no tiene proveedor, backend, organización ni entorno preseleccionados, y ninguna de esas decisiones vive aquí: viven en cada repo objetivo. No las inventes: confírmalas en el repositorio donde trabajes o pregúntalo.
