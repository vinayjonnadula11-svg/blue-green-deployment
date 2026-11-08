# Blue-Green Deployment Using Docker and Kubernetes

## Project Overview

This project demonstrates a Blue-Green Deployment strategy using Docker and Kubernetes to deploy and manage two different versions of a website.

The main objective is to reduce deployment risks by maintaining two separate application environments: Blue (Version 1.0) and Green (Version 2.0). Both versions run simultaneously, while Kubernetes Service controls which version receives application traffic.

This approach allows a new version to be deployed and tested before switching production traffic. If an issue occurs, traffic can be switched back to the previous version.

## Project Objectives

- Implement a Blue-Green Deployment strategy.
- Build separate Docker images for Blue and Green versions.
- Deploy both versions using Kubernetes Deployments.
- Manage application Pods using Kubernetes.
- Configure a Kubernetes NodePort Service.
- Switch traffic between Blue and Green using labels and selectors.
- Perform rollback by switching traffic to the previous version.

## Technologies Used

| Technology | Purpose |
|---|---|
| HTML & CSS | Website development |
| Docker | Containerization |
| Nginx | Web server |
| Kubernetes | Container orchestration |
| Minikube | Local Kubernetes cluster |
| PowerShell | Command-line operations |

## Project Architecture

```text
                Website Source Code
                         |
               +---------+---------+
               |                   |
          Blue Version         Green Version
            v1.0                  v2.0
               |                   |
          Docker Build        Docker Build
               |                   |
       blue-green-site       green-green-site
           :1.0                  :2.0
               |                   |
       Blue Deployment      Green Deployment
          2 Replicas            2 Replicas
               |                   |
               +---------+---------+
                         |
                 Kubernetes Service
                   NodePort 30085
                         |
                Traffic Management
                         |
              Blue / Green Switching
```

**Project Architecture Screenshot**

<!-- Add your architecture screenshot below -->

![Project Architecture](images/project-architecture.png)

## Project Folder Structure

```text
blue-green-deployment/
│
├── blue/
│   ├── index.html
│   └── Dockerfile
│
├── green/
│   ├── index.html
│   └── Dockerfile
│
├── k8s/
│   ├── blue-deployment.yaml
│   ├── green-deployment.yaml
│   └── service.yaml
│
├── images/
│   └── (Project screenshots)
│
└── README.md
```

**Project Folder Structure Screenshot**

<!-- Add your VS Code folder structure screenshot below -->

![Project Folder Structure](images/folder-structure.png)

## Implementation Process

### 1. Create Two Website Versions

Created two independent website versions:

- **Blue Version 1.0:** Existing production website.
- **Green Version 2.0:** Updated website prepared for deployment.

Each version has its own HTML file and Dockerfile.

**Screenshots – Blue and Green Websites**

<!-- Add Blue website screenshot -->

![Blue Version 1.0](images/blue-website.png)

<!-- Add Green website screenshot -->

![Green Version 2.0](images/green-website.png)

### 2. Create Docker Images

Used Nginx Alpine as the base image to serve the website.

Dockerfile:

```dockerfile
FROM nginx:alpine
COPY index.html /usr/share/nginx/html/index.html
EXPOSE 80
```

Built separate images:

```powershell
docker build -t blue-green-site:1.0 .
```

```powershell
docker build -t green-green-site:2.0 .
```

**Docker Image Build Screenshot**

<!-- Add Docker build output screenshot -->

![Docker Build](images/docker-build.png)

**Docker Images Screenshot**

<!-- Add docker images command output screenshot -->

![Docker Images](images/docker-images.png)

### 3. Load Images into Minikube

Made the locally built Docker images available to the Minikube Kubernetes cluster.

```powershell
minikube image load blue-green-site:1.0
minikube image load green-green-site:2.0
```

**Minikube Image Loading Screenshot**

<!-- Add image loading output screenshot -->

![Minikube Image Loading](images/minikube-image-load.png)

### 4. Deploy Blue Version

Created a Kubernetes Deployment named `blue-deployment`.

Configuration:

- Replicas: 2
- Image: `blue-green-site:1.0`
- Label: `version: blue`
- Container Port: 80
- Readiness Probe: HTTP `/`

Applied the manifest:

```powershell
kubectl apply -f k8s/blue-deployment.yaml
```

**Blue Deployment Screenshot**

<!-- Add Blue Deployment YAML or kubectl output screenshot -->

![Blue Deployment](images/blue-deployment.png)

### 5. Deploy Green Version

Created another Kubernetes Deployment named `green-deployment`.

Configuration:

- Replicas: 2
- Image: `green-green-site:2.0`
- Label: `version: green`
- Container Port: 80
- Readiness Probe: HTTP `/`

Applied the manifest:

```powershell
kubectl apply -f k8s/green-deployment.yaml
```

Both deployments run independently in the Kubernetes cluster.

**Green Deployment Screenshot**

<!-- Add Green Deployment YAML or kubectl output screenshot -->

![Green Deployment](images/green-deployment.png)

**Kubernetes Pods Screenshot**

<!-- Add kubectl get pods output screenshot showing both versions -->

![Kubernetes Pods](images/kubernetes-pods.png)

### 6. Configure Kubernetes Service

Created a Kubernetes NodePort Service named `blue-green-service`.

The Service provides a stable endpoint to access the application Pods and routes traffic based on labels and selectors.

Service configuration:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: blue-green-service
spec:
  type: NodePort
  selector:
    app: blue-green-site
    version: blue
  ports:
    - port: 80
      targetPort: 80
      nodePort: 30085
```

Applied the Service:

```powershell
kubectl apply -f k8s/service.yaml
```

**Kubernetes Service Screenshot**

<!-- Add service.yaml or kubectl get svc screenshot -->

![Kubernetes Service](images/kubernetes-service.png)

### 7. Blue-to-Green Traffic Switching

Initially, the Kubernetes Service selector points to:

```yaml
version: blue
```

After deploying and validating Green Version 2.0, updated the Service selector to:

```yaml
version: green
```

This switches Service traffic from the Blue Pods to the Green Pods without changing the Deployments.

**Blue-to-Green Switching Screenshot**

<!-- Add screenshot showing Green selector and Green website in browser -->

![Blue to Green Switching](images/blue-to-green.png)

### 8. Rollback: Green-to-Blue

If the Green version encounters an issue, the previous production version can be restored by changing the Service selector back:

```yaml
version: blue
```

This redirects traffic to the Blue Pods.

**Green-to-Blue Rollback Screenshot**

<!-- Add screenshot showing Blue selector and Blue website in browser -->

![Green to Blue Rollback](images/green-to-blue-rollback.png)

## Why Kubernetes Service Is Important

The Kubernetes Service is an important component of this project because:

- It provides a stable network endpoint for application Pods.
- It routes traffic using labels and selectors.
- It avoids accessing individual Pod IP addresses directly.
- It allows switching between Blue and Green versions.
- It makes rollback easier by changing the Service selector.

## Verification Commands

Check Deployments:

```powershell
kubectl get deployments
```

Check Pods:

```powershell
kubectl get pods --show-labels
```

Check Service:

```powershell
kubectl get services
```

Check Service endpoints:

```powershell
kubectl get endpoints blue-green-service
```

Access the website:

```powershell
minikube service blue-green-service
```

**Final Kubernetes Verification Screenshot**

<!-- Add screenshot showing deployments, pods, services and endpoints -->

![Kubernetes Verification](images/kubernetes-verification.png)

## Key Benefits

- Reduced deployment risk.
- Independent application versions.
- Easy traffic switching.
- Faster rollback to the previous version.
- Better understanding of Docker containerization and Kubernetes orchestration.
- Improved application deployment and management.

## Learning Outcomes

Through this project, I gained hands-on experience with:

- Building and managing Docker images.
- Deploying containerized applications on Kubernetes.
- Creating and managing Kubernetes Deployments and Pods.
- Configuring NodePort Services.
- Understanding labels, selectors and Service endpoints.
- Implementing Blue-Green traffic switching.
- Performing application rollback.

## Conclusion

Successfully implemented a Blue-Green Deployment strategy using Docker and Kubernetes. Deployed two independent website versions and used a Kubernetes Service to manage traffic between them.

This project demonstrates how deployment strategies can help reduce release risks and simplify application rollback in containerized environments.
