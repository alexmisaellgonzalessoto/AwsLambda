# Procesamiento de imágenes con Terraform y AWS

Proyecto académico basado en el archivo Mermaid de la actividad. La arquitectura debe poder desplegarse en DEV, QA y PROD con recursos y estados aislados.

Repositorio: https://github.com/alexmisaellgonzalessoto/AwsLambda

## Arquitectura revisada

Región indicada en el diagrama: `us-east-1`.

Flujo principal: cliente → API Gateway → Lambda de carga → S3 (`uploads/`) → SQS → Lambda de procesamiento → S3 (`processed/`). El resultado previsto es una imagen PNG circular de 40 × 40 píxeles con fondo transparente.

La arquitectura incluye una VPC, dos subredes públicas, dos privadas, dos NAT Gateway con sus direcciones IP elásticas, un Internet Gateway, endpoints de S3 y SQS, grupos de seguridad, roles IAM, una cola de errores, registros y alarma de CloudWatch. La acción de la alarma referencia un tema SNS.

## Estado del proyecto

Paso 1 completado: Mermaid revisado y repositorio inicializado.

Paso 2 en curso: AWS CLI y Terraform disponibles. No se encontraron perfiles de AWS configurados; la configuración del perfil y la comprobación de identidad están pendientes. No se ha creado infraestructura.

Las instrucciones de configuración, despliegue, validación y destrucción se incorporarán aquí conforme avance cada paso. Las credenciales y los estados de Terraform quedan fuera del repositorio. El archivo `.terraform.lock.hcl` se versionará cuando se genere.

## Requisitos comprobados

- Git instalado.
- AWS CLI: versión 2.37.7.
- Terraform: versión 1.15.8.
- Acceso a la cuenta AWS de la actividad: pendiente de configurar mediante un perfil.

## Perfil de AWS y comprobación de identidad

El método de configuración depende del acceso disponible: usuario IAM, credenciales temporales de laboratorio o IAM Identity Center. Se debe confirmar el método antes de configurar el perfil.

Las credenciales permanecen fuera del proyecto. En Windows, AWS CLI utiliza por defecto la carpeta `%USERPROFILE%\.aws` para sus archivos de configuración y credenciales. No se deben copiar esos archivos al repositorio.

Para consultar los perfiles existentes:

```powershell
aws configure list-profiles
```

Una vez configurado el perfil, sustituir `NOMBRE_DEL_PERFIL` por su nombre real y comprobar la identidad:

```powershell
aws sts get-caller-identity --profile NOMBRE_DEL_PERFIL --region us-east-1
```

El resultado debe mostrar la cuenta y la identidad utilizadas. Guardar una captura para la entrega sin mostrar claves ni tokens. Este comando consulta la identidad; no crea recursos.

Fuentes oficiales: [Configuración y perfiles de AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-files.html) y [Consulta de identidad con STS](https://docs.aws.amazon.com/cli/latest/reference/sts/get-caller-identity.html).

## Observaciones pendientes de resolver

- El Mermaid especifica `nodejs20.x`. AWS indica que quedó obsoleto el 30 de abril de 2026. Se debe resolver la versión antes de crear las funciones. [Entornos de ejecución de Lambda](https://docs.aws.amazon.com/lambda/latest/dg/lambda-runtimes.html).
- El diagrama anuncia cargas de 10 MB, pero la invocación síncrona de Lambda admite como máximo 6 MB; la codificación base64 añade tamaño. Se debe resolver este límite antes de implementar la carga. [Cuotas de Lambda](https://docs.aws.amazon.com/lambda/latest/dg/gettingstarted-limits.html).
