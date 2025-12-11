# Devsu Ejercicio Practico
Solución al reto técnico de Ingeniero Cloud, Arquitectura serverless en AWS con Lambda, API Gateway, DynamoDB y servicios administrados
# Reto Técnico - Ingeniero Cloud

Este repositorio contiene mi solución al reto técnico para la posición de Ingeniero Cloud. Diseñé una arquitectura completamente serverless en AWS que elimina la necesidad de gestionar servidores y escala automáticamente según la demanda.

## ¿Qué hay aquí?

- **`Reto-Tecnico-Ingeniero-Cloud.pdf`** 

## La arquitectura que propongo

Después de evaluar las opciones, decidí ir con una arquitectura serverless en AWS. La idea principal es aprovechar servicios administrados que escalan solos y solo cobran por lo que realmente se usa.

### Cómo funciona

Los usuarios acceden a través de CloudFront (la CDN de AWS), que distribuye el contenido desde el punto más cercano a ellos. El frontend está alojado en S3 como sitio estático, lo que es mucho más simple que mantener servidores web.

Cuando alguien hace una petición a la API, pasa por API Gateway que la enruta a la función Lambda correspondiente. Dividí la lógica en 4 funciones independientes:

- **Autenticación** - Maneja login y tokens con Cognito
- **CRUD Datos** - Operaciones básicas sobre DynamoDB
- **Procesamiento** - Lógica de negocio más compleja
- **Anexos** - Gestión de archivos en S3

Los datos se guardan en DynamoDB (base de datos NoSQL) y los archivos en S3. Todo protegido con WAF en el perímetro, IAM para permisos granulares, y monitoreado con CloudWatch y X-Ray.

### Por qué AWS

Elegí AWS principalmente porque Lambda es el servicio serverless más maduro del mercado. Lleva desde 2014 en producción y tiene integraciones nativas con más de 200 servicios. Además, el modelo de precios es ideal para proyectos nuevos: pagas solo por el tiempo de ejecución real, medido en milisegundos. Si no hay tráfico, el costo es casi cero.

También me convenció el ecosistema integrado. API Gateway, Lambda, DynamoDB, S3, Cognito... todo funciona junto sin necesidad de código de integración adicional.

### Seguridad

Implementé seguridad en múltiples capas:

- WAF filtra tráfico malicioso antes de que llegue a la aplicación
- Cognito maneja autenticación con tokens JWT que expiran en 1 hora
- Cada función Lambda tiene solo los permisos mínimos necesarios (IAM)
- Todo el tráfico va por HTTPS
- Los datos se cifran automáticamente en reposo y en tránsito
- CloudTrail audita todas las operaciones

### Escalabilidad

Lo interesante de esta arquitectura es que escala automáticamente sin configuración. Si de repente llegan mil peticiones simultáneas, Lambda crea las instancias necesarias. Cuando baja el tráfico, se reducen solas. No hay que configurar auto-scaling groups ni nada de eso.

DynamoDB también escala automáticamente. Lo configuré en modo on-demand, que ajusta la capacidad según el tráfico real.

### Monitoreo

CloudWatch recopila logs y métricas de todo. Configuré alarmas para:

- Tasa de errores 
- Latencia 
- Costos que excedan el presupuesto

X-Ray me permite ver el recorrido completo de cada petición a través de todos los servicios, lo que facilita mucho encontrar cuellos de botella.

## Contenido del documento

El PDF incluye:

**Parte teórica:**
1. Diferencias entre nube pública, privada e híbrida
2. Tres prácticas de seguridad importantes
3. Qué es Infrastructure as Code y herramientas (Terraform, CloudFormation, CDK)
4. Métricas esenciales para monitoreo
5. Docker y sus componentes
6. Caso Practico
