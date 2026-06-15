# IAM y principio de mínimo privilegio

## Principio de mínimo privilegio (least privilege)

El principio de mínimo privilegio establece que un usuario, rol o servicio debe contar únicamente con los permisos estrictamente necesarios para llevar a cabo su función, sin permisos adicionales "por si acaso".

Aplicar este principio es importante porque limita el impacto de posibles errores o de una credencial comprometida. Si una identidad solo tiene permisos para leer datos de un bucket S3 concreto, un atacante que obtenga esas credenciales solo podrá leer ese bucket, no eliminar bases de datos, modificar la red o crear nuevos usuarios. Cuantos menos permisos innecesarios existan, menor es la superficie de ataque y menor el daño potencial en caso de fallo.

## Políticas basadas en identidad vs. basadas en recursos

**Política basada en identidad (identity-based policy)**: se asocia directamente a un usuario, grupo o rol de IAM, y define qué acciones puede realizar esa identidad sobre qué recursos. Es la forma más habitual de gestionar permisos: por ejemplo, adjuntar la política `ReadOnlyAccess` a un usuario para que pueda consultar recursos pero no modificarlos.

**Política basada en recursos (resource-based policy)**: se asocia directamente al recurso (por ejemplo, un bucket S3, una cola SQS o una función Lambda), y define qué identidades —propias o de otras cuentas— pueden acceder a ese recurso y de qué forma.

¿Cuándo usar cada una?
- Las políticas basadas en identidad son la opción por defecto para gestionar qué puede hacer cada usuario, grupo o rol dentro de la propia cuenta.
- Las políticas basadas en recursos se usan principalmente cuando se necesita conceder acceso a un recurso desde otra cuenta de AWS (acceso cross-account), o cuando el control de acceso tiene más sentido definido "desde el recurso" que "desde la identidad" (por ejemplo, una política de bucket que permite el acceso público de lectura a ciertos objetos).

## Roles de IAM vs. usuarios de IAM

Un **usuario de IAM** representa a una persona o aplicación, y tiene credenciales a largo plazo (contraseña para la consola y/o claves de acceso para la CLI/API) asociadas de forma permanente a esa identidad.

Un **rol de IAM** es una identidad sin credenciales propias permanentes. En su lugar, puede ser "asumido" temporalmente por otra entidad (un usuario, un servicio de AWS, o incluso una cuenta externa), que recibe credenciales temporales con una caducidad determinada.

Las instancias EC2 utilizan roles en lugar de usuarios para acceder a otros servicios de AWS por varias razones:

- **Seguridad**: si se usaran las claves de acceso de un usuario dentro de la instancia, esas credenciales permanentes quedarían almacenadas en el servidor, y si la instancia se viera comprometida, el atacante obtendría credenciales válidas indefinidamente.

- **Rotación automática**: las credenciales temporales que proporciona un rol asociado a una instancia EC2 se renuevan automáticamente de forma periódica, sin intervención manual.

- **Sin gestión de secretos**: no es necesario almacenar, distribuir ni proteger ningún Access Key/Secret Key dentro de la instancia; AWS gestiona todo el proceso de forma transparente a través del servicio de metadatos de la instancia.

Por estas razones, asociar un rol de IAM a una instancia EC2 es la forma recomendada de dar acceso a esa instancia a otros servicios de AWS, como S3.

## Usuario de solo lectura: asir-readonly

Para verificar el funcionamiento de las políticas de mínimo privilegio, se ha creado un usuario adicional `asir-readonly` con la política administrada `ReadOnlyAccess`. Este usuario puede consultar y listar recursos en toda la cuenta (por ejemplo, `aws s3 ls` para listar buckets), pero no puede crear, modificar ni eliminar ningún recurso, ya que `ReadOnlyAccess` solo concede permisos de tipo `Get*`, `List*` y `Describe*`.
