\# Lab 06 - Kubernetes Fundamentals Answers



## Task 1.2 - Kubernetes System Components



| Observed Pod | Kubernetes Component | Type / Location | Explanation |

|---|---|---|---|

| kube-apiserver-minikube | API Server | Control Plane | Receives and processes requests made using kubectl and the Kubernetes API. |

| etcd-minikube | etcd | Control Plane | Stores the cluster configuration and state data. |

| kube-scheduler-minikube | Scheduler | Control Plane | Selects a suitable node for newly created Pods. |

| kube-controller-manager-minikube | Controller Manager | Control Plane | Runs controllers that compare the desired state with the actual state. |

| kube-proxy-rz7d7 | kube-proxy | Worker Node | Manages network rules and Service communication on the node. |

| coredns-7d764666f9-2rp69 | CoreDNS | Cluster Add-on | Provides DNS name resolution for Services and Pods inside the cluster. |

| storage-provisioner | Storage Provisioner | Minikube Add-on | Creates and manages local storage volumes for the Minikube cluster. |



The kubelet does not appear as a Pod because it runs directly on the node as a system service. It communicates with the control plane and makes sure that the required containers are running. The container runtime also runs directly on the node rather than as a normal Kubernetes Pod.



Minikube uses a single-node cluster. Therefore, the same Minikube node performs both control-plane and worker-node functions.







## Checkpoint Q1 - Control Plane and Worker Node



The control plane manages the Kubernetes cluster. It makes decisions about scheduling, stores the cluster state, receives API requests, and checks whether the actual state matches the desired state.



A worker node runs the application workloads. It contains components such as the kubelet, kube-proxy, and container runtime, which are responsible for running and networking the Pods.



In this Minikube environment, one node performs both the control-plane and worker-node roles.



## Checkpoint Q2 - Pod Ephemeral Behaviour



Before deleting the frontend Pod, its IP address was `10.244.0.4`.



After deleting and recreating the Pod using the same YAML manifest, its new IP address was `10.244.0.5`.



The IP address changed because Kubernetes Pods are ephemeral. When a Pod is deleted, that specific Pod instance and its network identity are removed. Recreating it creates a new Pod instance, and Kubernetes assigns a new IP address to it. Therefore, applications should not depend on a Pod's individual IP address.



## Checkpoint Q3 - Kubernetes Self-Healing Control Loop

The desired state of the frontend Deployment was three running Pods.

1. Kubernetes initially had three frontend Pods running, so the actual state matched the desired state.
2. I manually deleted one of the frontend Pods.
3. The actual state then temporarily became two Pods, while the desired state remained three Pods.
4. The Deployment controller continuously watched the cluster and detected this difference.
5. Kubernetes reconciled the difference by creating a new replacement Pod.
6. The replacement Pod became Running in approximately 25 seconds.
7. The actual state returned to three running Pods, matching the desired state again.

This demonstrates Kubernetes self-healing through its continuous control-loop model.



## Checkpoint Q4 - Independent Scaling

The frontend can be scaled independently because each application tier is deployed as a separate Kubernetes object.

The frontend, API, cache, and database have their own Deployments or StatefulSet. Therefore, changing the number of frontend replicas only affects the frontend Deployment.

The database tier does not need to be changed because it is managed separately. Kubernetes allows each service to scale according to its own workload and resource requirements.



## Checkpoint Q5 - Port Forward and Kubernetes Service

Port-forward provides a temporary direct connection from a local computer port to one specific Pod. It works only while the port-forward command is running.

A Kubernetes Service provides a stable network endpoint for a group of Pods selected using labels. The Service can send traffic to any available frontend Pod.

Services are important because Pods are ephemeral. When a Pod is deleted and recreated, its IP address may change. The Service keeps the same name and endpoint, so users and other applications do not need to know the individual Pod IP addresses.



## Checkpoint Q6 - Rolling Updates and Rollbacks

Kubernetes performs rolling updates by gradually replacing old Pods with new Pods. This allows the application to remain available during the update.

Kubernetes also stores the previous Deployment revision, so it can quickly roll back to the earlier working image if the new version causes a problem.

With Docker Compose alone, this process would be harder to perform safely because it does not automatically manage gradual Pod replacement, health-based rollout progress, revision history, or a built-in rollback command. The containers may need to be stopped and recreated manually, which can cause downtime and increase the risk of errors.



## Checkpoint Q7 - Deployment and StatefulSet

The frontend and API tiers use Deployments because they are stateless applications. Their Pods do not need permanent identities or dedicated persistent storage. Kubernetes can replace, restart, or scale these Pods in any order.

The PostgreSQL database uses a StatefulSet because it is a stateful application. The StatefulSet provides a stable Pod name, such as `postgres-0`, and maintains an ordered identity for the database Pod.

The PostgreSQL StatefulSet also uses a PersistentVolumeClaim. This keeps the database data separate from the Pod, so the data can remain available even if the PostgreSQL Pod is deleted and recreated.

## Checkpoint Q8 - Database Persistence

The PostgreSQL data survived because the StatefulSet used a PersistentVolumeClaim. The database files were stored in persistent storage instead of only inside the Pod.

When `postgres-0` was deleted, the StatefulSet created a new Pod with the same stable name. The new Pod mounted the same persistent volume, so the `lab6 test row` was still available.

If PostgreSQL had been deployed without a PersistentVolumeClaim, the data stored inside the container filesystem would normally be lost when the Pod was deleted and recreated.



## Checkpoint Q9 - Observability and Troubleshooting

The broken Pod entered the `ImagePullBackOff` state because Kubernetes could not download the image `nginx:definitely-not-a-real-tag`.

I first used `kubectl get pod broken-pod` to identify the Pod status. Then I used `kubectl describe pod broken-pod` to inspect the Events section.

The Events showed `Failed to pull image`, `manifest unknown`, `ErrImagePull`, and `Back-off pulling image`. This confirmed that the specified image tag did not exist.

The problem can be fixed by changing the image to a valid tag, such as `nginx:alpine`, and applying the corrected YAML file again. `kubectl logs` would not provide useful logs because the container never started successfully.
