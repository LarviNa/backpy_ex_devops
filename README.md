# Backend Python - Servicio de Productos (Contenedores & CI/CD)

Servicio de catálogo y gestión de productos en Python/Flask con MySQL para la **Evaluación Final Transversal (EFT)** de la asignatura **Introducción a Herramientas Devops**.

---

## Contenerización (Docker)

La contenerización del microservicio está diseñada bajo rigurosos criterios de seguridad:

1. **Imagen Minimalista**: Utiliza la distribución oficial `python:3.11-slim` para reducir al mínimo el tamaño de la imagen y los vectores de ataque.
2. **Hardening (Seguridad)**: Configurado bajo un usuario de sistema dedicado sin privilegios de administración (`appuser`), evitando ejecutar el proceso Flask como root.
3. **Caché y Optimización**: Instalación optimizada de pip sin caché para no generar peso innecesario.

### Construcción Local de la Imagen
```bash
docker build -t backend-products:latest .
```

### Ejecutar Contenedor
```bash
docker run -d -p 8082:8082 \
  -e DB_HOST=host.docker.internal \
  -e DB_PORT=3306 \
  -e DB_USER=root \
  -e DB_PASSWORD=rootpassword \
  -e DB_NAME=eval_db \
  backend-products:latest
```

---

## GitHub Actions - Pipeline CI/CD

El pipeline automatizado realiza las siguientes fases:

1. **Pruebas y Análisis de Sintaxis**: Ejecuta el chequeo de compilación de Python (`python -m py_compile app.py`) tras instalar las dependencias.
2. **Docker Build & Push**: Construye y sube la imagen Docker con versión única (`sha`) y la etiqueta `latest` a Docker Hub.
3. **AWS ECS Deploy**: Actualiza de forma segura la Task Definition de ECS e inicia el despliegue automático en el clúster de Fargate.

### Configuración de Secretos en GitHub

* `DOCKERHUB_USERNAME`: Tu usuario de Docker Hub.
* `DOCKERHUB_TOKEN`: Tu personal access token de Docker Hub.
* `AWS_ACCESS_KEY_ID`: Credenciales de acceso de AWS IAM con permisos en ECS.
* `AWS_SECRET_ACCESS_KEY`: Clave secreta de acceso de AWS IAM.
