# Fundamentos teóricos: Cloud Computing y AWS

## ¿Qué es la computación en la nube?

La computación en la nube (cloud computing) consiste en acceder a recursos informáticos —servidores, almacenamiento, bases de datos, redes— a través de internet, en lugar de poseer y mantener infraestructura física propia. Un proveedor como AWS gestiona los centros de datos y el hardware, y el cliente paga únicamente por los recursos que consume.

Esto supone un cambio respecto al modelo tradicional (servidores propios o "on-premise"): los recursos se pueden crear, ampliar, reducir o eliminar en minutos, sin necesidad de comprar, instalar o mantener hardware físico. Se conoce como infraestructura elástica.

## Modelos de servicio: IaaS, PaaS y SaaS

Estos tres modelos representan distintos niveles de abstracción y responsabilidad sobre la infraestructura:

- **IaaS (Infrastructure as a Service)**: el proveedor ofrece la infraestructura básica —máquinas virtuales, almacenamiento, redes— pero el usuario es responsable de instalar y configurar el sistema operativo, las aplicaciones y todo el software. Es el modelo con más control y más responsabilidad para el usuario.

- **PaaS (Platform as a Service)**: el proveedor gestiona la infraestructura y el sistema operativo, ofreciendo una plataforma lista para que el usuario despliegue su código directamente, sin preocuparse del servidor subyacente.

- **SaaS (Software as a Service)**: el proveedor ofrece una aplicación completa y funcional, lista para usar. El usuario no gestiona nada técnico, solo utiliza el software.

**AWS EC2 se enmarca en el modelo IaaS**, ya que proporciona máquinas virtuales (instancias) sobre las que el usuario instala y configura su propio sistema operativo, software y aplicaciones.

## Modelo de responsabilidad compartida

AWS opera bajo un modelo de responsabilidad compartida: AWS es responsable de la seguridad **de** la nube, y el cliente es responsable de la seguridad **en** la nube.

**AWS gestiona:**
- La infraestructura física global (centros de datos, hardware, red troncal).
- La virtualización y el aislamiento entre clientes.
- La disponibilidad y redundancia física de los servicios.

**El cliente (administrador) gestiona:**
- El sistema operativo de sus instancias: actualizaciones, parches y configuración.
- La configuración de red: VPC, subredes, security groups, reglas de firewall.
- La gestión de identidades y permisos (IAM): quién puede acceder a qué recursos.
- Sus propios datos: cifrado, copias de seguridad, control de acceso.

En resumen, AWS garantiza que la infraestructura subyacente funcione y esté disponible, pero la seguridad de lo que se construye encima depende totalmente de cómo se configure.

## Conceptos clave

- **Región (Region)**: ubicación geográfica de AWS que agrupa varios centros de datos (ej: `eu-west-1` en Irlanda, `eu-south-2` en España). Cada región es independiente de las demás.

- **Zona de disponibilidad (Availability Zone, AZ)**: dentro de una región, son centros de datos físicamente separados con energía, refrigeración y conectividad independientes, pero interconectados con baja latencia. Distribuir recursos entre varias AZs aumenta la tolerancia a fallos.

- **VPC (Virtual Private Cloud)**: red privada virtual aislada dentro de AWS, donde se despliegan los recursos (instancias, bases de datos, etc.). Permite definir un rango de direcciones IP propio y controlar completamente la topología de red.

- **Subred (Subnet)**: subdivisión de una VPC asociada a una AZ concreta. Puede ser:
  - **Pública**: tiene una ruta hacia un Internet Gateway, por lo que sus recursos pueden tener conectividad directa con internet.
  - **Privada**: no tiene ruta directa a internet, por lo que sus recursos solo son accesibles desde dentro de la VPC.

- **Grupo de seguridad (Security Group)**: actúa como un firewall virtual asociado a una o varias instancias. Define reglas de entrada (inbound) y salida (outbound) que controlan qué tráfico de red está permitido, especificando puertos, protocolos y orígenes/destinos.

- **AMI (Amazon Machine Image)**: plantilla que contiene la configuración necesaria para lanzar una instancia EC2, incluyendo el sistema operativo y, opcionalmente, software preinstalado. Ejemplo: Ubuntu Server 22.04 LTS.

- **Instancia EC2 (Elastic Compute Cloud)**: máquina virtual creada a partir de una AMI, que se ejecuta en la infraestructura de AWS. Es el equivalente a un servidor en la nube, con CPU, memoria, almacenamiento y red configurables.

## Por qué no usar la cuenta raíz para el día a día

La cuenta raíz de AWS es la identidad con la que se crea la cuenta y tiene acceso ilimitado a absolutamente todo: facturación, eliminación de la cuenta, cambio de configuraciones de seguridad, y todos los servicios sin restricción alguna. Por este motivo, usarla para tareas diarias es una mala práctica de seguridad por varias razones:

- **Riesgo desproporcionado**: si las credenciales de la cuenta raíz se ven comprometidas (por ejemplo, por phishing o una filtración), el atacante tiene control total sobre la cuenta, incluyendo la facturación y la posibilidad de eliminar todos los recursos.

- **Falta de trazabilidad**: cuando varias personas comparten la cuenta raíz, no se puede saber quién hizo qué cambio. Con usuarios IAM individuales, cada acción queda asociada a una identidad concreta.

- **Imposibilidad de aplicar permisos limitados**: la cuenta raíz no se puede restringir; siempre tiene acceso completo. Los usuarios IAM, en cambio, permiten aplicar el principio de mínimo privilegio.

Por estas razones, la recomendación de AWS es: usar la cuenta raíz únicamente para tareas que requieren explícitamente sus permisos (como cambiar el plan de soporte o cerrar la cuenta), activar MFA en ella inmediatamente, y crear un usuario IAM administrador (como `asir-admin`) para el trabajo diario.

## AWS Free Tier (nivel gratuito)

Durante los primeros 12 meses tras la creación de una cuenta de AWS, ciertos servicios están disponibles de forma gratuita dentro de unos límites mensuales:

- **EC2**: 750 horas/mes de instancias `t2.micro` (o `t3.micro` según la región), lo que equivale a poder mantener una instancia funcionando de forma continua durante todo el mes.

- **S3**: 5 GB de almacenamiento estándar, junto con 20.000 solicitudes de lectura (GET) y 2.000 solicitudes de escritura (PUT) al mes.

- **RDS**: 750 horas/mes de instancias `db.t3.micro` (o `db.t2.micro`), 20 GB de almacenamiento de base de datos y 20 GB adicionales para backups automáticos.

Una vez superados estos límites, o transcurridos los 12 meses, el uso de estos servicios se factura según las tarifas estándar de AWS. Es recomendable revisar periódicamente el panel de facturación (Billing Dashboard) para evitar cargos inesperados.
