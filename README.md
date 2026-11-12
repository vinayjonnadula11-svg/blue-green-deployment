# 🔵🟢 Blue-Green Deployment Using Docker and Kubernetes

## 📌 Project Overview

This project demonstrates the implementation of a **Blue-Green Deployment Strategy using Docker and Kubernetes**. It was developed as part of my **College Final-Year Project** to gain practical experience in containerization, application deployment, Kubernetes orchestration, and deployment strategies.

Blue-Green Deployment is a deployment technique in which two separate application environments are maintained:

- 🔵 **Blue Environment – Version 1.0:** Represents the existing production version of the application.
- 🟢 **Green Environment – Version 2.0:** Represents the updated version of the application.

Both versions are deployed independently using Docker containers and Kubernetes Deployments. A Kubernetes Service is used to control which environment receives application traffic by changing its selector.

This approach helps demonstrate application updates, traffic switching, and rollback to a previous version with minimal downtime.

## 🎯 Project Objectives

- Understand and implement the Blue-Green Deployment strategy.
- Build and containerize website applications using Docker.
- Deploy multiple application versions using Kubernetes.
- Configure Kubernetes Deployments and Services.
- Switch application traffic between Blue and Green environments.
- Demonstrate rollback to the previous application version.
- Gain hands-on experience with container orchestration.

## 🛠️ Technologies Used

| Technology | Purpose |
|---|---|
| HTML | Creating the application webpages |
| Docker | Building and containerizing application images |
| Kubernetes | Managing application deployments and Pods |
| Minikube | Running a local Kubernetes cluster |
| YAML | Defining Kubernetes resources |
| Docker Desktop | Providing the local container environment |
| Windows PowerShell | Executing Docker and Kubernetes commands |

## 🏗️ Project Architecture

The project consists of two independent application environments connected through a Kubernetes Service.

```text
                   User / Browser
                         |
                         |
                Kubernetes Service
                  NodePort: 30085
                         |
                  Service Selector
                         |
              +----------+----------+
              |                     |
         Blue Environment      Green Environment
           Version 1.0           Version 2.0
              |                     |
       Blue Deployment       Green Deployment
              |                     |
         2 Kubernetes           2 Kubernetes
             Pods                  Pods
              |                     |
        Blue Website           Green Website
```

**Traffic Switching:**

- Initially, the Service selector points to the Blue environment.
- When the selector is changed to Green, traffic is routed to the Green Pods.
- When the selector is changed back to Blue, traffic returns to the Blue Pods.

## 🔄 Project Implementation Workflow

### 1. Website Development

Created two simple website versions:

- Blue website displaying **BLUE DEPLOYMENT – Production Environment – Version 1.0**.
- Green website displaying **GREEN DEPLOYMENT – New Production Environment – Version 2.0**.

Different background colours and headings were used to identify each environment easily.

### 2. Docker Image Creation

Created separate Dockerfiles for the Blue and Green applications and built individual Docker images.

Docker images used:

- `blue-green-site:1.0`
- `green-green-site:2.0`

This allowed both website versions to run independently in containers.

### 3. Kubernetes Deployments

Created separate Kubernetes Deployment YAML files for both application versions.

- **blue-deployment.yaml:** Deploys the Blue application with 2 replicas.
- **green-deployment.yaml:** Deploys the Green application with 2 replicas.

Each Deployment uses labels to identify its application version:

- Blue: `version: blue`
- Green: `version: green`

Both environments can run simultaneously in the Kubernetes cluster.

### 4. Kubernetes Service Configuration

Created a Kubernetes Service named `blue-green-service`.

Service configuration:

- **Service Type:** NodePort
- **Service Port:** 80
- **Target Port:** 80
- **NodePort:** 30085
- **Application Label:** `app: blue-green-site`

The Service provides a consistent access point for the application. Its selector determines which version of the application receives traffic.

### 5. Blue Environment Deployment

Initially configured the Kubernetes Service selector to:

`version: blue`

The Service routes traffic to the Blue Deployment Pods, displaying the existing production website (Version 1.0).

### 6. Green Environment Deployment

Changed the Kubernetes Service selector to:

`version: green`

The Service then routes traffic to the Green Deployment Pods, displaying the updated website (Version 2.0).

### 7. Rollback to Blue

Changed the Service selector back to:

`version: blue`

This demonstrates how traffic can be switched back to the previous application version without deleting the Green Deployment.

## 📸 Project Screenshots

### 🔵 1. Blue Deployment – Version 1.0

Shows the Blue application running in the browser. This represents the existing production environment and displays Version 1.0.

<img width="1767" height="842" alt="Screenshot 2026-11-08 162225" src="https://github.com/user-attachments/assets/ffa7bcaa-59e8-498b-8a9b-e1506ba6880e" />


### 🟢 2. Green Deployment – Version 2.0

Shows the updated Green application running in the browser. This represents the new production environment and displays Version 2.0.

<img width="1417" height="690" alt="image" src="https://github.com/user-attachments/assets/664f8220-8fc9-4ace-89ad-150cbbf437a7" />


### ⚙️ 3. Kubernetes Service – Blue Environment

Shows the Kubernetes Service configuration with the selector `app=blue-green-site,version=blue`. The Service is configured to route traffic to the Blue Deployment Pods.

<img width="703" height="341" alt="Screenshot 2026-11-08 162053" src="https://github.com/user-attachments/assets/c3a5bc6f-302f-4188-a343-c5c6087f03e5" />


### 🔄 4. Kubernetes Service – Green Environment

Shows the Kubernetes Service configuration with the selector `app=blue-green-site,version=green`. This demonstrates changing the Service selector to route traffic to the Green environment.

<img width="682" height="366" alt="image" src="https://github.com/user-attachments/assets/4a93eb0c-bb7b-41cc-8553-90416215a17f" />



## 📂 Project Folder Structure

```text
blue-green/
│
├── blue/
│   ├── dockerfile
│   └── index.html
│
├── green/
│   ├── dockerfile
│   └── index.html
│
├── k8s/
│   ├── blue-deployment.yaml
│   ├── green-deployment.yaml
│   └── service.yaml
│
├── images/
│   ├── blue-deployment.png
│   ├── green-deployment.png
│   ├── service-blue.png
│   └── service-green.png
│
└── README.md
```

## 💻 Important Kubernetes Commands

**Apply Kubernetes Deployments:**

```powershell
kubectl apply -f .\k8s\blue-deployment.yaml
kubectl apply -f .\k8s\green-deployment.yaml
```

**Create the Kubernetes Service:**

```powershell
kubectl apply -f .\k8s\service.yaml
```

**Check Deployments and Pods:**

```powershell
kubectl get deployments
kubectl get pods -o wide
```

**Check Services:**

```powershell
kubectl get services
kubectl describe service blue-green-service
```

**Switch Traffic to Green:**

```powershell
kubectl patch service blue-green-service --type=merge --patch-file .\green-patch.yaml
```

**Rollback Traffic to Blue:**

```powershell
kubectl patch service blue-green-service --type=merge --patch-file .\blue-patch.yaml
```

**Verify Service Endpoints:**

```powershell
kubectl get endpoints blue-green-service
```

**Access the Application:**

```powershell
minikube service blue-green-service
```

## ✨ Key Features

- Two independent application environments.
- Separate Docker images for different application versions.
- Multiple Kubernetes Deployments running simultaneously.
- Kubernetes Service-based traffic switching.
- Version-based Pod selection using labels and selectors.
- Demonstration of rollback to the previous version.
- Minimal-downtime deployment approach.

## 📚 Learning Outcomes

Through this project, I gained practical knowledge of:

- Docker image creation and containerization.
- Kubernetes Pods, Deployments, and Services.
- YAML configuration and resource management.
- Kubernetes labels and selectors.
- Application traffic routing.
- Blue-Green Deployment and rollback strategies.
- Managing application versions in a Kubernetes environment.

## 👨‍💻 Project Information

| Category | Details |
|---|---|
| Project Name | Blue-Green Deployment |
| Project Type | Self-Initiated Project |
| Deployment Strategy | Blue-Green Deployment |
| Containerization | Docker |
| Orchestration | Kubernetes |
| Local Cluster | Minikube |
| Application | HTML Website |
| Operating Environment | Windows 11 / Docker Desktop |

---

## 🙌 Conclusion

This project provided hands-on experience in implementing a Blue-Green Deployment strategy using Docker and Kubernetes. By maintaining two independent application environments and controlling traffic through Kubernetes Service selectors, I learned how application versions can be deployed, switched, and rolled back in a controlled manner.

**Developed as part of my Self-Initiated Project.**
