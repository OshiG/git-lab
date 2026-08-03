\# Lab 06 - Kubernetes Fundamentals



\## Overview



This lab demonstrates the basic concepts of Kubernetes using Minikube and Docker Desktop.



The lab includes creating Pods, Deployments, Services, rolling updates, rollbacks, scaling, self-healing, multi-tier application deployment, persistent storage, monitoring, troubleshooting, and cleanup.



\## Technologies Used



\- Docker Desktop

\- Minikube

\- Kubernetes

\- kubectl

\- Windows PowerShell



\## Application Architecture



The application contains the following tiers:



\- Frontend: Nginx

\- API: HTTPBin

\- Cache: Redis

\- Database: PostgreSQL



The frontend is exposed using a NodePort Service.



The API and cache use ClusterIP Services for internal communication.



PostgreSQL is deployed using a StatefulSet with persistent storage.



\## Repository Structure



```text

lab6/

├── k8s/

│   ├── pod-frontend.yaml

│   ├── deployment-frontend.yaml

│   ├── service-frontend.yaml

│   ├── api-deployment.yaml

│   ├── api-service.yaml

│   ├── cache-deployment.yaml

│   ├── cache-service.yaml

│   ├── postgres-statefulset.yaml

│   └── postgres-service.yaml

├── screenshots/

│   ├── task-1.1.png

│   ├── task-2.1.png

│   ├── task-3.1.png

│   ├── task-4.1.png

│   ├── task-5.1.png

│   ├── task-6.1.png

│   ├── task-7.1.png

│   ├── task-7.2.png

│   ├── task-8.1.png

│   ├── task-9.1.png

│   └── task-10.1.png

├── answers.md

└── README.md

