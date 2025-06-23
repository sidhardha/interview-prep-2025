# Kubernetes & Azure Kubernetes Service (AKS) Architecture Guide

---

## 1. Kubernetes Core Components

### Control Plane Components
- **API Server (`kube-apiserver`)**: Central management point, exposes Kubernetes API.
- **etcd**: Distributed key-value store for cluster state.
- **Scheduler (`kube-scheduler`)**: Assigns pods to nodes based on resource requirements and policies.
- **Controller Manager (`kube-controller-manager`)**: Runs controllers (e.g., node, replication, endpoints).

**Diagram:**
```
[User]
  |
[API Server] -- [etcd]
  |      |         |
[Scheduler] [Controller Manager]
```

**Sample API Server Manifest:**
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: kube-apiserver
  namespace: kube-system
spec:
  containers:
  - name: kube-apiserver
    image: k8s.gcr.io/kube-apiserver:v1.29.0
    command:
    - kube-apiserver
    # ...other flags and configs...
```

### Node Components
- **kubelet**: Agent on each node, manages pod lifecycle.
- **Container Runtime**: (e.g., containerd, Docker) Runs containers.
- **kube-proxy**: Maintains network rules for pod communication.

**Sample Kubelet Manifest:**
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: kubelet
  namespace: kube-system
spec:
  containers:
  - name: kubelet
    image: k8s.gcr.io/kubelet:v1.29.0
    # ...config...
```

**References:**
- [Kubernetes Components](https://kubernetes.io/docs/concepts/overview/components/)

---

## 2. Kubernetes Resource Management

### Pods & Deployments
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.25
        ports:
        - containerPort: 80
```

### StatefulSets
```yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: web
spec:
  serviceName: "nginx"
  replicas: 2
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.25
        volumeMounts:
        - name: www
          mountPath: /usr/share/nginx/html
  volumeClaimTemplates:
  - metadata:
      name: www
    spec:
      accessModes: [ "ReadWriteOnce" ]
      resources:
        requests:
          storage: 1Gi
```

### DaemonSets
```yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: fluentd
spec:
  selector:
    matchLabels:
      name: fluentd
  template:
    metadata:
      labels:
        name: fluentd
    spec:
      containers:
      - name: fluentd
        image: fluent/fluentd:v1.16
```

### Services & Ingress
```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  selector:
    app: nginx
  ports:
  - protocol: TCP
    port: 80
    targetPort: 80
  type: ClusterIP
```

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: example-ingress
spec:
  rules:
  - host: example.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: nginx-service
            port:
              number: 80
```

### Network Policies
```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-nginx
spec:
  podSelector:
    matchLabels:
      app: nginx
  policyTypes:
  - Ingress
  ingress:
  - from:
    - podSelector:
        matchLabels:
          access: "true"
```

### Storage Classes, PV/PVCs
```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: azure-disk-sc
provisioner: kubernetes.io/azure-disk
parameters:
  skuName: Standard_LRS
```

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: azure-disk-pvc
spec:
  accessModes:
    - ReadWriteOnce
  storageClassName: azure-disk-sc
  resources:
    requests:
      storage: 5Gi
```

### ConfigMaps & Secrets
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: app-config
  namespace: default
  labels:
    app: myapp
  data:
    LOG_LEVEL: "info"
```

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: db-secret
  namespace: default
  labels:
    app: myapp
  type: Opaque
  data:
    password: cGFzc3dvcmQ=
```

### Resource Quotas & Limits
```yaml
apiVersion: v1
kind: ResourceQuota
metadata:
  name: compute-resources
spec:
  hard:
    pods: "10"
    requests.cpu: "4"
    requests.memory: 8Gi
    limits.cpu: "8"
    limits.memory: 16Gi
```

**References:**
- [Kubernetes API Reference](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.29/)

---

## 3. AKS-Specific Features

### Cluster Architecture & Node Pools
- **System node pool:** Runs system pods (e.g., kube-proxy, CoreDNS).
- **User node pools:** Run application workloads; can use different VM sizes.

**Azure CLI Example:**
```powershell
az aks create --resource-group myRG --name myAKS --node-count 3 --enable-addons monitoring --generate-ssh-keys
az aks nodepool add --resource-group myRG --cluster-name myAKS --name userpool --node-count 2 --node-vm-size Standard_DS3_v2
```

### Authentication & RBAC
- Integrate with Azure AD for SSO and RBAC.
- Use Kubernetes RBAC for fine-grained access control.

**Azure CLI Example:**
```powershell
az aks update --resource-group myRG --name myAKS --enable-aad
```

### Networking Options
- **kubenet:** Basic, uses Azure VNet for nodes, pods get NAT IPs.
- **Azure CNI:** Pods get IPs from VNet, supports advanced networking.

**Azure CLI Example:**
```powershell
az aks create --resource-group myRG --name myAKS --network-plugin azure
```

### Storage Integration
- Use Azure Disks for block storage, Azure Files for shared storage.

**YAML Example:**
```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: azurefile-pvc
spec:
  accessModes:
    - ReadWriteMany
  storageClassName: azurefile
  resources:
    requests:
      storage: 5Gi
```

### Azure Monitor Integration
- Enable monitoring at cluster creation or via Azure Portal.

**Azure CLI Example:**
```powershell
az aks enable-addons --resource-group myRG --name myAKS --addons monitoring
```

### Azure AD Integration
- Use Azure AD for cluster authentication and Kubernetes RBAC.

**References:**
- [AKS Documentation](https://learn.microsoft.com/en-us/azure/aks/)
- [AKS ARM Templates](https://learn.microsoft.com/en-us/azure/templates/microsoft.containerservice/managedclusters)

---

## 4. Production Best Practices

### High Availability
- Use multiple node pools and Availability Zones.
- Distribute workloads across zones.

### Scaling Strategies
- Use Cluster Autoscaler and HPA (Horizontal Pod Autoscaler).

**YAML Example:**
```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: nginx-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: nginx-deployment
  minReplicas: 2
  maxReplicas: 10
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 50
```

### Security Hardening
- Enable Azure Policy for AKS.
- Use network policies, restrict public access, and scan images.
- Use managed identities and Key Vault for secrets.

### Monitoring & Logging
- Integrate with Azure Monitor and Log Analytics.
- Use Fluentd/Logstash for custom log shipping.

### Backup & Disaster Recovery
- Use Velero for backup/restore.
- Regularly test DR procedures.

### Cost Optimization
- Use spot node pools for non-critical workloads.
- Right-size node pools and use autoscaling.

**References:**
- [AKS Best Practices](https://learn.microsoft.com/en-us/azure/aks/best-practices)

---

## 5. Troubleshooting Guide

### Common Failure Scenarios
- Pod scheduling failures (insufficient resources, taints/tolerations)
- Network connectivity issues
- Image pull errors

### Debugging Techniques
- `kubectl describe pod <pod>`
- `kubectl logs <pod>`
- `kubectl get events`
- `kubectl exec -it <pod> -- /bin/sh`

### Performance Optimization
- Use resource requests/limits
- Profile with `kubectl top` and Azure Monitor

### Log Analysis
- Use `kubectl logs`, Azure Monitor, and Log Analytics

### Diagnostic Commands
```shell
kubectl get nodes
kubectl get pods -A
kubectl describe node <node>
kubectl get events --sort-by=.metadata.creationTimestamp
```

**References:**
- [Kubernetes Troubleshooting](https://kubernetes.io/docs/tasks/debug/)
- [AKS Troubleshooting](https://learn.microsoft.com/en-us/azure/aks/troubleshooting/)

---

## Kubernetes Services: Detailed Explanation, Diagram, and Examples

Kubernetes Services provide stable networking endpoints to access a set of Pods, abstracting away the dynamic nature of Pod IPs. Services enable communication within the cluster and with external clients.

### Why Services?
- Pods are ephemeral; their IPs can change when rescheduled.
- Services provide a consistent DNS name and IP address to access a group of Pods selected by labels.

### Types of Kubernetes Services

1. **ClusterIP (default):**
   - Exposes the Service on an internal IP in the cluster.
   - Accessible only within the cluster.
   - Use case: Internal app-to-app communication.

2. **NodePort:**
   - Exposes the Service on a static port on each node’s IP.
   - Accessible from outside the cluster using `<NodeIP>:<NodePort>`.
   - Use case: Simple external access for development/testing.

3. **LoadBalancer:**
   - Provisions an external load balancer (cloud provider required).
   - Routes external traffic to the Service.
   - Use case: Production-grade external access.

4. **ExternalName:**
   - Maps the Service to a DNS name (external resource).
   - Use case: Accessing external databases/services via a Service.

### How Services Work
- Services use label selectors to match Pods.
- Endpoints are automatically updated as Pods come and go.
- kube-proxy manages routing rules on nodes.

### Diagram: Kubernetes Service Types
```
[Client]
   |
[LoadBalancer Service] (External IP)
   |
[NodePort Service] (Node IP:Port)
   |
[ClusterIP Service] (Cluster IP)
   |
[Pods] (Selected by labels)
```

### Example 1: ClusterIP Service
```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  selector:
    app: nginx
  ports:
  - protocol: TCP
    port: 80
    targetPort: 80
  type: ClusterIP
```
- Accessed within the cluster as `nginx-service:80`.

### Example 2: NodePort Service
```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-nodeport
spec:
  selector:
    app: nginx
  ports:
  - protocol: TCP
    port: 80
    targetPort: 80
    nodePort: 30080
  type: NodePort
```
- Accessed externally as `<NodeIP>:30080`.

### Example 3: LoadBalancer Service (Cloud)
```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-lb
spec:
  selector:
    app: nginx
  ports:
  - protocol: TCP
    port: 80
    targetPort: 80
  type: LoadBalancer
```
- Cloud provider assigns an external IP for access.

### Example 4: ExternalName Service
```yaml
apiVersion: v1
kind: Service
metadata:
  name: external-db
spec:
  type: ExternalName
  externalName: db.example.com
```
- Accessing an external database as `external-db` within the cluster.

### Key Points
- Services decouple consumers from the underlying Pods.
- DNS names are automatically created for Services (e.g., `nginx-service.default.svc.cluster.local`).
- Services can be combined with Ingress for advanced HTTP routing.

**References:**
- [Kubernetes Services Documentation](https://kubernetes.io/docs/concepts/services-networking/service/)

---

## End-to-End Architecture: Java Microservices Backend, JavaScript Frontend, and SQL Database on AKS (Production Best Practices)

This section explains the architecture, components, and communication flows for a production-grade deployment of a Java microservices backend, a JavaScript frontend, and a SQL database on Azure Kubernetes Service (AKS). It covers best practices, required Azure resources, and secure service-to-service and service-to-database communication.

### 1. High-Level Architecture Diagram
```
[User]
  |
[Azure Application Gateway / Azure Front Door]
  |
[AKS Ingress Controller (NGINX/AGIC)]
  |
[JavaScript Frontend Service (React/Angular/Vue)]
  |
[Java Microservices Backend (Spring Boot, Quarkus, etc.)]
  |
[Azure SQL Database / Managed Instance]

[Azure Key Vault] <---+--- [AKS Pods: Secrets, DB Credentials, API Keys]
[Azure Monitor/Log Analytics] <---+--- [AKS Cluster, Apps, DB]
[Azure Storage Account] <---+--- [Persistent Volumes, Blob/File Storage]
```

---

### 2. Key Components and Their Roles

#### a. AKS Cluster
- Hosts all application workloads (frontend, backend, supporting services).
- Uses multiple node pools (system/user, spot/regular) for isolation and cost optimization.
- Enabled with Azure AD integration, RBAC, and network policies.

#### b. Ingress Controller
- NGINX or Azure Application Gateway Ingress Controller (AGIC) for HTTP routing, SSL termination, and WAF.
- Exposes frontend and backend services securely.

#### c. JavaScript Frontend (SPA)
- Deployed as a containerized app (e.g., NGINX serving static files) or via Azure Static Web Apps.
- Communicates with backend via HTTPS REST/gRPC APIs.

#### d. Java Microservices Backend
- Multiple stateless microservices (Spring Boot, Quarkus, Micronaut, etc.), each in its own Deployment.
- Exposes REST/gRPC APIs, protected by OAuth2/JWT (Azure AD, Entra ID, or Auth0).
- Uses ConfigMaps for non-secret config and Key Vault for secrets.
- Service-to-service communication via ClusterIP Services and DNS.
- Uses Azure Managed Identities for secure access to Azure resources.

#### e. SQL Database
- Azure SQL Database or Managed Instance for high availability, backup, and scaling.
- Private endpoint or VNet integration for secure access from AKS.
- Uses Azure Key Vault for connection strings and credentials.

#### f. Azure Key Vault
- Stores secrets, certificates, and keys (DB credentials, API keys, TLS certs).
- Accessed by microservices using managed identities.

#### g. Azure Storage Account
- Used for persistent storage (e.g., file uploads, logs, backups).
- Mounted as Persistent Volumes (Azure Files/Disks) in AKS.

#### h. Monitoring & Logging
- Azure Monitor, Log Analytics, and Application Insights for cluster/app monitoring, alerting, and tracing.

#### i. Security & Compliance
- Network Policies for pod-to-pod isolation.
- Azure Policy for AKS for compliance enforcement.
- Pod Security Standards (non-root, seccomp, etc.).
- Image scanning (ACR Tasks, Trivy, etc.).

---

### 3. Communication Flows

#### a. User to Frontend
- User accesses the app via a public endpoint (App Gateway/Front Door).
- HTTPS traffic is routed to the Ingress Controller, then to the frontend service.

#### b. Frontend to Backend
- Frontend (JavaScript SPA) calls backend APIs via HTTPS (secured by OAuth2/JWT).
- Ingress routes API requests to the correct backend microservice.

#### c. Service-to-Service (Backend Microservices)
- Microservices communicate via internal ClusterIP Services using DNS (e.g., `orders-service.default.svc.cluster.local`).
- Network Policies restrict which services can talk to each other.
- Secrets/configs are pulled from Key Vault using managed identities.

#### d. Backend to SQL Database
- Backend microservices connect to Azure SQL Database via private endpoint (no public IP exposure).
- Connection strings and credentials are securely retrieved from Key Vault.
- Managed identities can be used for passwordless authentication.

#### e. Service to Storage
- Microservices use Azure Storage SDKs to read/write blobs/files, authenticating via managed identities.

#### f. Monitoring & Logging
- All logs and metrics are sent to Azure Monitor/Log Analytics.
- Alerts and dashboards are configured for proactive monitoring.

---

### 4. Example YAML Snippets

#### a. Ingress Resource
```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: app-ingress
  annotations:
    kubernetes.io/ingress.class: nginx
spec:
  tls:
  - hosts:
    - myapp.example.com
    secretName: tls-secret
  rules:
  - host: myapp.example.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: frontend-service
            port:
              number: 80
      - path: /api/
        pathType: Prefix
        backend:
          service:
            name: backend-service
            port:
              number: 8080
```

#### b. Service-to-Service Communication
```yaml
apiVersion: v1
kind: Service
metadata:
  name: orders-service
spec:
  selector:
    app: orders
  ports:
  - protocol: TCP
    port: 8080
    targetPort: 8080
  type: ClusterIP
```
- Microservices use DNS: `orders-service.default.svc.cluster.local`

#### c. Azure Key Vault Integration (AAD Pod Identity)
- Microservice Deployment uses a managed identity to access Key Vault:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: backend-service
spec:
  template:
    spec:
      containers:
      - name: backend
        image: myacr.azurecr.io/backend:latest
        env:
        - name: DB_CONNECTION_STRING
          valueFrom:
            secretKeyRef:
              name: db-conn-secret
              key: connectionString
      # Azure Workload Identity or AAD Pod Identity setup here
```

#### d. Persistent Volume for Storage
```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: fileshare-pvc
spec:
  accessModes:
    - ReadWriteMany
  storageClassName: azurefile
  resources:
    requests:
      storage: 10Gi
```

---

## Example: Stateful Service, Volumes, and Azure Storage Integration in AKS

### 1. What is a Stateful Service?
A stateful service is an application that maintains persistent data across restarts and pod rescheduling. Examples include databases (PostgreSQL, MySQL, MongoDB), message queues, and file storage services. In Kubernetes, stateful services are typically managed using StatefulSets, which provide stable network identities and persistent storage for each pod.

### 2. StatefulSet Example with Persistent Volumes
```yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: postgres
spec:
  serviceName: "postgres"
  replicas: 2
  selector:
    matchLabels:
      app: postgres
  template:
    metadata:
      labels:
        app: postgres
    spec:
      containers:
      - name: postgres
        image: postgres:15
        ports:
        - containerPort: 5432
        env:
        - name: POSTGRES_PASSWORD
          valueFrom:
            secretKeyRef:
              name: postgres-secret
              key: password
        volumeMounts:
        - name: pgdata
          mountPath: /var/lib/postgresql/data
  volumeClaimTemplates:
  - metadata:
      name: pgdata
    spec:
      accessModes: [ "ReadWriteOnce" ]
      storageClassName: azure-disk-sc
      resources:
        requests:
          storage: 10Gi
```
- Each pod gets its own PersistentVolumeClaim (PVC) and PersistentVolume (PV), ensuring data is not lost if the pod is rescheduled.

### 3. Azure Storage Options for AKS

#### a. Azure Disks
- Block storage, best for single-pod access (e.g., databases).
- High performance and durability.
- Used via PVCs with `storageClassName: azure-disk-sc`.

#### b. Azure Files
- Shared file storage, supports ReadWriteMany (RWX) access.
- Suitable for shared data, logs, or uploads across multiple pods.
- Used via PVCs with `storageClassName: azurefile`.

#### c. Azure Blob Storage
- Object storage for unstructured data (images, backups, large files).
- Accessed via Azure SDKs or CSI drivers (not as a native PV, but as an external service).

### 4. Example: Azure Files PVC and Pod Mount
```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: shared-files-pvc
spec:
  accessModes:
    - ReadWriteMany
  storageClassName: azurefile
  resources:
    requests:
      storage: 20Gi
---
apiVersion: v1
kind: Pod
metadata:
  name: file-app
spec:
  containers:
  - name: app
    image: busybox
    command: ["sleep", "3600"]
    volumeMounts:
    - name: shared-files
      mountPath: /mnt/files
  volumes:
  - name: shared-files
    persistentVolumeClaim:
      claimName: shared-files-pvc
```
- This pod can read/write files in `/mnt/files`, and the data is persisted in Azure Files.

### 5. Azure Storage Classes in Kubernetes
```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: azure-disk-sc
provisioner: kubernetes.io/azure-disk
parameters:
  skuName: Premium_LRS
---
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: azurefile
provisioner: kubernetes.io/azure-file
parameters:
  skuName: Standard_LRS
```

### 6. Best Practices for Storage in AKS
- Use Azure Disks for databases and single-writer workloads.
- Use Azure Files for shared storage needs.
- Use managed identities or Kubernetes secrets for secure access to Azure Storage.
- Regularly back up persistent data (e.g., with Velero or Azure Backup).
- Use private endpoints for secure, VNet-integrated storage access.
- Monitor storage performance and capacity with Azure Monitor.

### 7. Diagram: Stateful Service and Storage Integration
```
[StatefulSet]
   |
[Pod 0] -- [PVC 0] -- [Azure Disk 0]
[Pod 1] -- [PVC 1] -- [Azure Disk 1]

[Pod(s)] -- [PVC] -- [Azure Files Share] (for shared storage)
```

**References:**
- [Kubernetes StatefulSets](https://kubernetes.io/docs/concepts/workloads/controllers/statefulset/)
- [Azure Disks for AKS](https://learn.microsoft.com/en-us/azure/aks/azure-disks-dynamic-pv)
- [Azure Files for AKS](https://learn.microsoft.com/en-us/azure/aks/azure-files-dynamic-pv)
- [Azure Blob Storage](https://learn.microsoft.com/en-us/azure/storage/blobs/)
- [Kubernetes Storage Classes](https://kubernetes.io/docs/concepts/storage/storage-classes/)

---

## Kubernetes Interview Questions and Answers for Senior Cloud Engineering Roles

### 1. Explain the difference between a Deployment, StatefulSet, and DaemonSet. When would you use each?
**Answer:**
- **Deployment:** Manages stateless applications, provides rolling updates, scaling, and self-healing. Use for web servers, APIs, etc.
- **StatefulSet:** Manages stateful applications, provides stable network IDs, persistent storage, and ordered deployment. Use for databases, message queues.
- **DaemonSet:** Ensures a copy of a pod runs on all (or some) nodes. Use for log collectors, monitoring agents.

---

### 2. How would you design a highly available application on AKS?
**Answer:**
- Use multiple node pools and Availability Zones.
- Deploy at least 3 replicas of each service.
- Use LoadBalancer or Ingress for traffic distribution.
- Store state in managed services (e.g., Azure SQL, Cosmos DB) or use StatefulSets with persistent storage.
- Enable pod anti-affinity to spread pods across nodes/zones.
- Use liveness/readiness probes for health checks.

---

### 3. Scenario: A pod is stuck in Pending state. How do you troubleshoot?
**Answer:**
- Check `kubectl describe pod <pod>` for events.
- Common causes: insufficient resources, missing PVC, node selector/taint mismatch.
- Check node status: `kubectl get nodes`.
- Check PVC and storage class status if using volumes.
- Use `kubectl get events --sort-by=.metadata.creationTimestamp` for recent errors.

---

### 4. How do you secure communication between microservices in Kubernetes?
**Answer:**
- Use Network Policies to restrict pod-to-pod traffic.
- Use mTLS (e.g., Istio, Linkerd) for encrypted service-to-service communication.
- Use RBAC for API access control.
- Store secrets in Kubernetes Secrets or integrate with Azure Key Vault.
- Use private endpoints for databases and storage.

---

### 5. Scenario: You need to perform a zero-downtime deployment. How do you achieve this?
**Answer:**
- Use Deployments with rolling updates (`kubectl rollout`).
- Set appropriate readiness/liveness probes.
- Use `maxUnavailable` and `maxSurge` in rolling update strategy.
- Use Ingress or Service to route traffic only to ready pods.
- Monitor rollout status and rollback if needed.

---

### 6. How do you handle secrets management in Kubernetes for production?
**Answer:**
- Use Kubernetes Secrets for sensitive data.
- Integrate with Azure Key Vault via CSI driver for external secret management.
- Use RBAC to restrict secret access.
- Avoid storing secrets in code or container images.
- Use encryption at rest for secrets.

---

### 7. Scenario: A node goes down. What happens to the pods? How does Kubernetes recover?
**Answer:**
- Kubelet on the node becomes unresponsive.
- Controller Manager marks node as NotReady after timeout.
- Pods on the node are marked as Unknown, then deleted.
- Scheduler reschedules pods to healthy nodes.
- StatefulSet pods retain their PVCs and data.

---

### 8. How do you implement resource limits and requests? Why are they important?
**Answer:**
- Set `resources.requests` and `resources.limits` in pod specs.
- Requests guarantee minimum resources; limits cap maximum usage.
- Prevents resource starvation and noisy neighbor issues.
- Enables efficient bin-packing and autoscaling.

---

### 9. Scenario: You need to restrict egress traffic from a namespace. How do you do it?
**Answer:**
- Use Network Policies with `policyTypes: [Egress]`.
- Define allowed destinations (e.g., specific IPs, namespaces).
- Deny all other egress by default.
- Example:
```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: deny-egress
spec:
  podSelector: {}
  policyTypes:
  - Egress
  egress: []
```

---

### 10. How do you monitor and log Kubernetes workloads in production?
**Answer:**
- Use Azure Monitor, Log Analytics, and Application Insights for AKS.
- Use Fluentd/Logstash/Prometheus/Grafana for custom logging and metrics.
- Enable audit logging for the API server.
- Set up alerts and dashboards for proactive monitoring.

---

### 11. Scenario: You need to perform a database migration with zero downtime. How do you approach this in Kubernetes?
**Answer:**
- Use a blue-green or canary deployment for the app.
- Apply schema changes in a backward-compatible way.
- Use readiness probes to control traffic cutover.
- Monitor for errors before switching all traffic.
- Rollback if issues are detected.

---

### 12. How do you handle multi-tenant workloads securely in AKS?
**Answer:**
- Use namespaces for logical isolation.
- Apply Network Policies to restrict cross-namespace traffic.
- Use Azure AD and RBAC for access control.
- Use resource quotas and limits per namespace.
- Separate node pools if needed for compliance.

---

### 13. Scenario: You need to autoscale your application based on custom metrics. How do you implement this?
**Answer:**
- Use Horizontal Pod Autoscaler (HPA) with custom metrics (e.g., via Prometheus Adapter).
- Define metric queries and thresholds.
- Ensure metrics server or custom adapter is deployed.
- Example:
```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: custom-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: my-app
  minReplicas: 2
  maxReplicas: 10
  metrics:
  - type: Pods
    pods:
      metric:
        name: custom_metric
      target:
        type: AverageValue
        averageValue: 100
```

---

### 14. How do you upgrade a running AKS cluster with minimal disruption?
**Answer:**
- Use AKS upgrade commands to upgrade control plane and node pools.
- Upgrade node pools one at a time, cordon and drain nodes.
- Use PodDisruptionBudgets to control pod eviction.
- Monitor workloads and roll back if issues occur.

**Detailed Explanation:**

Upgrading an AKS cluster with minimal disruption is a multi-step, production-critical process. Here’s how to do it safely:

#### 1. Upgrade Process Overview
- **Control Plane Upgrade:** Upgrades the Kubernetes API server and core controllers. Managed by Azure, usually non-disruptive to running workloads, but API requests may be briefly unavailable.
- **Node Pool Upgrade:** Upgrades the Kubernetes version on worker nodes (VMs). This is where most workload disruption risk lies.

#### 2. Step-by-Step Upgrade Strategy

**A. Plan the Upgrade**
- Review [AKS release notes](https://learn.microsoft.com/en-us/azure/aks/release-notes) for breaking changes.
- Check compatibility of workloads, CRDs, and add-ons.
- Backup critical data (etcd, persistent volumes) and cluster configuration.

**B. Upgrade the Control Plane**
- Use Azure CLI or Portal:
  ```powershell
  az aks upgrade --resource-group <rg> --name <aks-name> --kubernetes-version <new-version>
  ```
- Azure manages the control plane upgrade. Running workloads are not restarted, but API access may be briefly interrupted.

**C. Upgrade Node Pools (One at a Time)**
- Upgrade each node pool individually:
  ```powershell
  az aks nodepool upgrade --resource-group <rg> --cluster-name <aks-name> --name <pool-name> --kubernetes-version <new-version>
  ```
- Azure will cordon (mark unschedulable) and drain (evict pods) from each node, then replace it with a new VM running the new version.
- For critical workloads, you can manually cordon/drain nodes for more control:
  ```shell
  kubectl cordon <node>
  kubectl drain <node> --ignore-daemonsets --delete-emptydir-data
  ```

**D. Use PodDisruptionBudgets (PDBs)**
- Define PDBs to ensure a minimum number of pods remain available during upgrades:
  ```yaml
  apiVersion: policy/v1
  kind: PodDisruptionBudget
  metadata:
    name: myapp-pdb
  spec:
    minAvailable: 2
    selector:
      matchLabels:
        app: myapp
  ```
- PDBs prevent too many pods from being evicted at once, maintaining service availability.

**E. Monitor and Validate**
- Monitor pod status, application health, and logs during the upgrade.
- Use readiness/liveness probes to ensure only healthy pods receive traffic.
- Validate workloads after each node pool upgrade.

**F. Rollback if Needed**
- If issues are detected, pause further upgrades.
- Roll back node pool or redeploy previous workloads as needed.

#### 3. Best Practices
- **Stagger Upgrades:** Upgrade node pools sequentially, not all at once.
- **Test in Staging:** Always test upgrades in a non-production environment first.
- **Automate with CI/CD:** Use pipelines to automate and validate upgrades.
- **Use Multiple Node Pools:** Distribute workloads for safer, rolling upgrades.
- **Leverage Availability Zones:** Spread node pools across zones for resilience.

#### 4. Diagram: Upgrade Flow
```
[Control Plane Upgrade]
        |
[Node Pool 1] --[cordon/drain]--> [Upgrade] --[validate]--> [Uncordon]
        |
[Node Pool 2] --[cordon/drain]--> [Upgrade] --[validate]--> [Uncordon]
        |
[...repeat for all pools...]
```

#### 5. Common Interview Scenarios
- **Q:** What happens if a PDB blocks node drain?
  - **A:** The upgrade pauses until enough pods are available for eviction, ensuring availability.
- **Q:** How do you minimize downtime for stateful workloads?
  - **A:** Use PDBs, readiness probes, and ensure persistent storage is not disrupted.

#### 6. References
- [AKS Upgrade Documentation](https://learn.microsoft.com/en-us/azure/aks/upgrade-cluster)
- [PodDisruptionBudget](https://kubernetes.io/docs/tasks/run-application/configure-pdb/)

---

### Pod Anti-Affinity: Spreading Pods Across Nodes

Pod anti-affinity ensures that replicas of a workload are scheduled on different nodes or zones, improving high availability and fault tolerance. This is critical for production-grade deployments in AKS and other enterprise Kubernetes clusters.

**Example: Deployment with Pod Anti-Affinity (Spread Across Zones and Nodes)**

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: web-backend
spec:
  replicas: 3
  selector:
    matchLabels:
      app: web-backend
  template:
    metadata:
      labels:
        app: web-backend
    spec:
      affinity:
        podAntiAffinity:
          preferredDuringSchedulingIgnoredDuringExecution:
          - weight: 100
            podAffinityTerm:
              labelSelector:
                matchExpressions:
                - key: app
                  operator: In
                  values:
                  - web-backend
              topologyKey: topology.kubernetes.io/zone
          - weight: 50
            podAffinityTerm:
              labelSelector:
                matchExpressions:
                - key: app
                  operator: In
                  values:
                  - web-backend
              topologyKey: kubernetes.io/hostname
      containers:
      - name: backend
        image: myacr.azurecr.io/web-backend:latest
        ports:
        - containerPort: 8080
```

**Explanation:**
- `podAntiAffinity` with `preferredDuringSchedulingIgnoredDuringExecution` tells the scheduler to prefer spreading pods across different zones (using `topology.kubernetes.io/zone`) and, secondarily, across different nodes (`kubernetes.io/hostname`).
- This increases resilience to both node and zone failures.
- For strict separation, use `requiredDuringSchedulingIgnoredDuringExecution` (only if you have enough nodes/zones).

**Best Practices:**
- Always use anti-affinity for critical, replicated workloads.
- Combine with multiple node pools and AKS Availability Zones for maximum resilience.
- Monitor pod distribution with `kubectl get pods -o wide`.

**References:**
- [Kubernetes Affinity and Anti-Affinity](https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/#affinity-and-anti-affinity)
- [AKS Availability Zones](https://learn.microsoft.com/en-us/azure/aks/availability-zones)

---

## Advanced Kubernetes Security: Network Policies, mTLS, and RBAC

### 1. Using Network Policies to Restrict Pod-to-Pod Traffic

**Overview:**
Network Policies in Kubernetes allow you to control which pods can communicate with each other and with resources outside the cluster. This is essential for zero-trust networking and microsegmentation in production AKS environments.

**Key Concepts:**
- By default, all pods can communicate with each other.
- Applying a NetworkPolicy to a pod restricts traffic to only what is explicitly allowed.
- Policies can control ingress (incoming) and egress (outgoing) traffic.

**Example: Allow Only Frontend to Access Backend**
```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-frontend-to-backend
spec:
  podSelector:
    matchLabels:
      app: backend
  policyTypes:
  - Ingress
  ingress:
  - from:
    - podSelector:
        matchLabels:
          app: frontend
```

**Deny All Egress Example:**
```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: deny-all-egress
spec:
  podSelector: {}
  policyTypes:
  - Egress
  egress: []
```

**Diagram:**
```
[frontend-pod] ---> [backend-pod]
     |                 ^
     +-----------------+
(Only allowed by policy)
```

**Best Practices:**
- Start with a default deny-all policy, then explicitly allow required traffic.
- Use namespaces and labels for fine-grained control.
- Regularly audit policies for least privilege.
- Test policies in staging before production rollout.
- Use Azure Policy for AKS to enforce network policy usage.

**Scenario Q&A:**
- *Q: How do you restrict a database pod to only accept traffic from a specific app?*
  - A: Use a NetworkPolicy with `podSelector` for the DB and `from` referencing the app's label.
- *Q: How do you block all egress except to Azure SQL?*
  - A: Use a deny-all egress policy, then add an egress rule for the Azure SQL IP/CIDR.

---

### 2. Using mTLS (Istio, Linkerd) for Encrypted Service-to-Service Communication

**Overview:**
mTLS (mutual TLS) ensures all service-to-service traffic is encrypted and authenticated. Service meshes like Istio and Linkerd automate certificate management and policy enforcement for mTLS in Kubernetes/AKS.

**Architecture Diagram:**
```
[Service A Pod] <-> [Sidecar Proxy] <====mTLS====> [Sidecar Proxy] <-> [Service B Pod]
         |                                         |
   [Control Plane: Istio/Linkerd]-------------------+
```

**Istio Example: Enable mTLS for a Namespace**
```yaml
apiVersion: security.istio.io/v1beta1
kind: PeerAuthentication
metadata:
  name: default
  namespace: my-namespace
spec:
  mtls:
    mode: STRICT
```

**Linkerd Example: Enable mTLS (Automatic)**
- Linkerd enables mTLS by default for all meshed pods. No extra YAML is needed, but you can enforce strict policies with `Server` and `ServerAuthorization` resources.

**Best Practices:**
- Use STRICT mode for production (all traffic must be mTLS).
- Rotate mesh certificates regularly (automated by mesh control plane).
- Monitor mesh health and certificate status.
- Use mesh policies to restrict which services can talk to each other (zero trust).
- For AKS, use managed identities for mesh control plane access to Azure resources.

**Scenario Q&A:**
- *Q: How do you ensure only certain services can communicate, even with mTLS?*
  - A: Use mesh AuthorizationPolicies (Istio) or ServerAuthorization (Linkerd) to restrict allowed principals.
- *Q: How do you troubleshoot mTLS failures?*
  - A: Check mesh control plane logs, pod sidecar logs, and certificate status. Use mesh dashboard for diagnostics.

---

### 3. Using RBAC for API Access Control (with Azure AD Integration)

**Overview:**
RBAC (Role-Based Access Control) in Kubernetes restricts what users, groups, and service accounts can do. In AKS, RBAC can be integrated with Azure AD for enterprise SSO and centralized access management.

**Key Concepts:**
- Roles define allowed actions (verbs) on resources.
- RoleBindings assign roles to users, groups, or service accounts.
- ClusterRoles/ClusterRoleBindings are for cluster-wide permissions.
- Azure AD integration allows mapping Azure users/groups to Kubernetes RBAC.

**Example: Read-Only Role for a Namespace**
```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: dev
  name: read-only
rules:
- apiGroups: ["*"]
  resources: ["*"]
  verbs: ["get", "list", "watch"]
---
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: read-only-binding
  namespace: dev
subjects:
- kind: User
  name: "user@yourdomain.com" # Azure AD user
roleRef:
  kind: Role
  name: read-only
  apiGroup: rbac.authorization.k8s.io
```

**Azure AD Integration Example:**
```powershell
az aks update --resource-group myRG --name myAKS --enable-aad
az aks get-credentials --resource-group myRG --name myAKS --admin
```

**Best Practices:**
- Grant least privilege—only the permissions required.
- Use Azure AD groups for access management, not individual users.
- Regularly audit RoleBindings and ClusterRoleBindings.
- Use managed identities for service accounts accessing Azure resources.
- Enable audit logging for all API access.

**Scenario Q&A:**
- *Q: How do you give a CI/CD pipeline access to deploy only in a specific namespace?*
  - A: Create a service account with a Role/RoleBinding scoped to that namespace, and use it in the pipeline.
- *Q: How do you map an Azure AD group to a Kubernetes role?*
  - A: Use Azure AD integration and reference the group object ID in the RoleBinding subject.

---

### 21. How do you revert a deployment in Kubernetes?
**Answer:**
- Use `kubectl rollout undo deployment/<deployment-name>` to revert to the previous revision.
- You can specify a particular revision with `--to-revision=<n>` if needed.
- Check rollout history with `kubectl rollout history deployment/<deployment-name>`.
- Monitor the status with `kubectl rollout status deployment/<deployment-name>`.

**Step-by-Step Example:**
1. View deployment history:
   ```shell
   kubectl rollout history deployment/my-app
   ```
2. Revert to the previous revision:
   ```shell
   kubectl rollout undo deployment/my-app
   ```
3. (Optional) Revert to a specific revision:
   ```shell
   kubectl rollout undo deployment/my-app --to-revision=2
   ```
4. Monitor the rollout:
   ```shell
   kubectl rollout status deployment/my-app
   ```

**Best Practices:**
- Always monitor application health after a rollback.
- Use readiness/liveness probes to ensure only healthy pods receive traffic.
- Keep deployments declarative and version-controlled (e.g., GitOps).
- Use `kubectl describe deployment/<deployment-name>` to troubleshoot if rollback fails.

**Scenario Q&A:**
- *Q: What happens if the rollback also fails?*
  - A: Kubernetes will keep the deployment in a failed state; you can roll forward to another revision or fix the manifest and apply again.
- *Q: How do you prevent accidental rollbacks?*
  - A: Use RBAC to restrict who can perform rollbacks and require approvals in CI/CD pipelines.

**References:**
- [Kubernetes Rollback Documentation](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#rolling-back-a-deployment)

---

### 22. Scenario: A deployment rollout is stuck. How do you troubleshoot and resolve it?
**Answer:**
- Check rollout status: `kubectl rollout status deployment/<deployment-name>`.
- Describe the deployment and pods for events: `kubectl describe deployment/<deployment-name>` and `kubectl describe pod <pod>`.
- Common causes: failed readiness/liveness probes, image pull errors, insufficient resources, or misconfigured selectors.
- Fix the underlying issue (e.g., update image, fix probes, increase resources) and re-apply the deployment.
- If needed, pause or undo the rollout: `kubectl rollout pause|resume|undo deployment/<deployment-name>`.

---

### 23. Scenario: You need to rotate secrets used by running pods. How do you do it safely?
**Answer:**
- Update the Kubernetes Secret with the new value: `kubectl apply -f secret.yaml`.
- Trigger a rolling restart of affected deployments to pick up the new secret:
  ```shell
  kubectl rollout restart deployment/<deployment-name>
  ```
- Use readiness/liveness probes to ensure only healthy pods receive traffic during the restart.
- For zero-downtime, use multiple replicas and PodDisruptionBudgets.

---

### 24. Scenario: A service is intermittently unreachable from other pods. What steps do you take?
**Answer:**
- Check pod and service endpoints: `kubectl get endpoints <service-name>`.
- Verify pod health and readiness: `kubectl get pods -o wide` and `kubectl describe pod <pod>`.
- Check for network policies or security groups blocking traffic.
- Review kube-proxy and CNI plugin logs for errors.
- Use `kubectl exec` to test connectivity from within a pod.
- Investigate DNS resolution issues with `nslookup` or `dig` inside the pod.

---

### 25. Scenario: You need to enforce that only images from a trusted registry are deployed. How do you achieve this?
**Answer:**
- Use an Admission Controller (e.g., Azure Policy for AKS, OPA/Gatekeeper) to restrict allowed image registries.
- Example Azure Policy: Deny deployments using images not from `myacr.azurecr.io`.
- Regularly audit running pods for image sources.
- Educate teams and enforce policy in CI/CD pipelines.

---

### 26. Scenario: A node in your AKS cluster is under heavy CPU pressure. What do you do?
**Answer:**
- Use `kubectl top node` and `kubectl top pod` to identify resource usage.
- Check for pods with high CPU requests/limits or runaway processes.
- Consider scaling the node pool or adding autoscaling.
- Evict or reschedule non-critical pods to other nodes.
- Use taints/tolerations and resource quotas to prevent resource contention in the future.

---

### 27. Scenario: You need to migrate a workload to a new namespace with minimal downtime. How do you proceed?
**Answer:**
- Deploy the workload in the new namespace with updated manifests.
- Migrate secrets, configmaps, and persistent volume claims as needed.
- Update service endpoints and DNS if required.
- Gradually shift traffic (e.g., via Ingress or Service) to the new namespace.
- Monitor application health and remove the old deployment after successful migration.

---

