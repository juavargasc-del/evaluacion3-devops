# Innovatech Chile - Plataforma de Gestión de Despachos

## Descripción del proyecto

Este proyecto corresponde a la Evaluación Parcial N°3 de la asignatura **DevOps**, cuyo objetivo es desplegar una aplicación basada en microservicios utilizando contenedores Docker y Kubernetes, además de automatizar la construcción y publicación de imágenes mediante GitHub Actions.

La solución está compuesta por tres servicios principales:

* **Frontend:** interfaz web de la aplicación.
* **Microservicio Ventas:** administra la información de ventas.
* **Microservicio Despachos:** administra la información de despachos.
* **Base de datos MySQL:** almacena la información utilizada por los microservicios.

Todos los componentes se encuentran desplegados dentro de un clúster Kubernetes (k3s) utilizando recursos Deployment y Service.

---

# Arquitectura

```
                    GitHub
                       │
                       │ Push (rama deploy)
                       ▼
               GitHub Actions
             (Build + Push Docker)
                       │
                       ▼
                  Docker Hub
                       │
                       ▼
          Kubernetes (k3s - Laboratorio)
                       │
        ┌──────────────┼──────────────┐
        │              │              │
        ▼              ▼              ▼
   Frontend        Ventas       Despachos
        │              │              │
        └──────────────┼──────────────┘
                       │
                       ▼
                    MySQL
```

---

# Tecnologías utilizadas

* Java 17
* Spring Boot 3
* Maven
* Docker
* Podman
* Kubernetes (k3s)
* Git
* GitHub
* GitHub Actions
* MySQL 8

---

# Estructura del proyecto

```
.
├── back-Ventas_SpringBoot/
├── back-Despachos_SpringBoot/
├── front_despacho/
├── k8s/
│   ├── namespace.yaml
│   ├── mysql-deployment.yaml
│   ├── mysql-service.yaml
│   ├── ventas-deployment.yaml
│   ├── ventas-service.yaml
│   ├── despachos-deployment.yaml
│   ├── despachos-service.yaml
│   ├── frontend-deployment.yaml
│   └── frontend-service.yaml
└── .github/
    └── workflows/
        └── deploy.yml
```

---

# Construcción de imágenes Docker

## Backend Ventas

```bash
podman build -t localhost:5000/innovatech-backend-ventas:eks-v1 \
back-Ventas_SpringBoot/Springboot-API-REST
```

## Backend Despachos

```bash
podman build -t localhost:5000/innovatech-backend-despachos:eks-v1 \
back-Despachos_SpringBoot/Springboot-API-REST-DESPACHO
```

## Frontend

```bash
podman build -t localhost:5000/innovatech-frontend:eks-v1 \
front_despacho
```

---

# Publicación de imágenes

```bash
podman push localhost:5000/innovatech-backend-ventas:eks-v1

podman push localhost:5000/innovatech-backend-despachos:eks-v1

podman push localhost:5000/innovatech-frontend:eks-v1
```

---

# Despliegue en Kubernetes

Todos los recursos Kubernetes se encuentran definidos en la carpeta **k8s**.

Para desplegar toda la aplicación:

```bash
kubectl apply -f k8s/
```

---

# Verificación del despliegue

Consultar los Deployments:

```bash
kubectl get deployments
```

Consultar los Services:

```bash
kubectl get services
```

Consultar los Pods:

```bash
kubectl get pods
```

Consultar todos los recursos:

```bash
kubectl get all
```

---

# Pipeline CI/CD

El proyecto incorpora un pipeline de Integración Continua mediante GitHub Actions.

El workflow se ejecuta automáticamente cuando se realiza un **push** sobre la rama:

```
deploy
```

El pipeline realiza las siguientes tareas:

* Descarga el código del repositorio.
* Inicia sesión en Docker Hub.
* Construye la imagen del microservicio Ventas.
* Publica la imagen de Ventas.
* Construye la imagen del microservicio Despachos.
* Publica la imagen de Despachos.
* Construye la imagen del Frontend.
* Publica la imagen del Frontend.

De acuerdo con las instrucciones del laboratorio, el despliegue sobre Kubernetes se realiza manualmente desde el servidor k3s mediante `kubectl apply`, por lo que el pipeline automatiza únicamente las etapas de **Build** y **Push**.

---

# Variables de entorno

Los microservicios utilizan las siguientes variables para conectarse a MySQL:

| Variable    | Descripción                |
| ----------- | -------------------------- |
| DB_ENDPOINT | Host de MySQL              |
| DB_PORT     | Puerto de MySQL            |
| DB_NAME     | Nombre de la base de datos |
| DB_USERNAME | Usuario de MySQL           |
| DB_PASSWORD | Contraseña de MySQL        |

---

# Autores

**Juan Fernando Vargas Castillo**
**Matilda Isabel Vargas Canseco**
Evaluación Parcial N°3 - DevOps

Duoc UC - 2026

