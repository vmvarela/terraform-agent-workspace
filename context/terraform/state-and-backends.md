# Estado y backends — guía genérica de referencia

Guía genérica para trabajar con estado de Terraform **en repositorios clonados bajo `projects/<repo>`**. El estado contiene todo el modelo de recursos y datos potencialmente sensibles: no lo versiones ni lo dejes accesible. La configuración real de backend vive en cada repo objetivo; esta guía es suplementaria y no prevalece sobre ella.

## Reglas generales

- Estado remoto para entornos compartidos, con locking nativo o añadido según defina el repo objetivo.
- Nunca commitees `*.tfstate`, `*.tfplan` ni backups de estado en ningún repositorio. `.terraform.lock.hcl` sí se versiona.
- Un backend por raíz; el repo objetivo define cómo separa entornos (stacks/workspaces/estructura de ficheros).

## La configuración real vive en el repo objetivo

Este workspace **no tiene backend propio ni config de ejemplo** a propósito: las decisiones de backend son de cada repositorio. En el repo donde trabajes, comprueba ahí:

- Tipo de backend que usa (S3+DynamoDB, Azure Blob, GCS, Terraform Cloud/Enterprise, etc.), bucket/contenedor y región.
- Claves de estado y convención de naming.
- Política de locking y de permisos mínimos sobre el bucket/contenedor.
- Gestión del drift y refresh-only.

Si el repo no lo documenta, pregúntalo; no lo deduzcas del nombre de ficheros.

## Operaciones delicadas (requieren revisión humana y plan de migración)

- `terraform state mv|rm|pull|push` — solo con plan de migración revisado por el usuario. Las migraciones entre backends o de estado compartido son especialmente delicadas: detente ante cualquier duda.
- `terraform import` — documenta el recurso importado y verifica con un plan vacío consecuente.
- Recuperación de desastres: conserva backups del estado remoto según la política del proveedor y del repo objetivo.
