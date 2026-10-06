# Procesamiento de imágenes con Terraform y AWS

Proyecto académico basado en el archivo Mermaid de la actividad. La arquitectura debe poder desplegarse en DEV, QA y PROD con recursos y estados aislados.

Repositorio: https://github.com/alexmisaellgonzalessoto/AwsLambda

## Arquitectura revisada

Región indicada en el diagrama: `us-east-1`.

Flujo principal: cliente → API Gateway → Lambda de carga → S3 (`uploads/`) → SQS → Lambda de procesamiento → S3 (`processed/`). El resultado previsto es una imagen PNG circular de 40 × 40 píxeles con fondo transparente.

La arquitectura incluye una VPC, dos subredes públicas, dos privadas, dos NAT Gateway con sus direcciones IP elásticas, un Internet Gateway, endpoints de S3 y SQS, grupos de seguridad, roles IAM, una cola de errores, registros y alarma de CloudWatch. La acción de la alarma referencia un tema SNS.

## Estado del proyecto

Paso 1: Mermaid revisado y repositorio inicializado. Todavía no se ha creado infraestructura ni ejecutado Terraform.

Las instrucciones de configuración, despliegue, validación y destrucción se incorporarán aquí conforme avance cada paso. Las credenciales y los estados de Terraform quedan fuera del repositorio. El archivo `.terraform.lock.hcl` se versionará cuando se genere.

## Observaciones pendientes de resolver

- El Mermaid especifica `nodejs20.x`. AWS indica que quedó obsoleto el 30 de abril de 2026. Se debe resolver la versión antes de crear las funciones. [Entornos de ejecución de Lambda](https://docs.aws.amazon.com/lambda/latest/dg/lambda-runtimes.html).
- El diagrama anuncia cargas de 10 MB, pero la invocación síncrona de Lambda admite como máximo 6 MB; la codificación base64 añade tamaño. Se debe resolver este límite antes de implementar la carga. [Cuotas de Lambda](https://docs.aws.amazon.com/lambda/latest/dg/gettingstarted-limits.html).
