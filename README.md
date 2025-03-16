# 1. Design Decisions
## 1.1 Use of Helm
Reason: Helm offers a convenient way to package, version, and manage Kubernetes resources. It supports environment-based overrides and is well-supported in the community.
Result: We maintain a simple Helm chart that defines all resources needed to run Vikunja (Deployments, Services, ConfigMaps, etc.). Each service (frontend, backend, database) is encapsulated in its own YAML template.
## 1.2 Separation of Concerns
Frontend and Backend each have their own Deployment, making it easier to scale and update them independently.
Database is separated into its own resource (StatefulSet) so that data management, backups, and scaling can be handled distinctly from the app logic.
## 1.3 Minimal External Dependencies
We only require a standard Kubernetes cluster with (optionally) an Ingress controller. Everything else (PostgreSQL, Keycloak) can be deployed in the same cluster or be externally managed.

# 2. Network & Performance Optimizations

## Load Balancing via Services

We use a Kubernetes Service to distribute traffic across multiple replicas of each component. This ensures that no single Pod becomes a bottleneck.

## Horizontal Pod Scaling

The Helm chart sets initial replicaCount to 2 or more (for both frontend and backend), enhancing availability and allowing for horizontal scaling when demands grow.

## Readiness & Liveness Probes

Each Deployment is configured with readiness and liveness probes so that unhealthy Pods are removed from service endpoints, improving reliability and reducing 504/timeout issues.

## Resource Requests/Limits

We define CPU/Memory requests and limits to avoid resource contention in noisy multi-tenant clusters. This ensures stable performance under load.

## Optional Ingress
An Ingress resource to simplify external access

# 3. Database Deployment Strategy
We offer two main approaches to handle Vikunja’s PostgreSQL database:

## Self-Hosted (StatefulSet)

Self-hosted for full control, easy to run locally, no external services needed.

# 4. Keycloak IAM Setup
Integrated with oficial Helm chart installation 