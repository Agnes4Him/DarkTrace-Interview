# DarkTrace-Interview

## Overiew

The architectural design created for this task is for a deployment of a 2-service application for a blog.

It consists of `user` and `posts` microservices.

A frontend component is also integrated to provide a user interface.

The architecture is based on following:

* Infrastrurture Provisioning with Infrastructure as Code (Terraform) to achieve version control, repeatability, consistency and speed

* Amazon Elastic Kubernetes Service - For the deployment of the microservices (API)

* Amazon Elastic Container Registry - For storing Docker images and scanning them for security vulnerabilities

* Amazon S3 Bucket and CloudFront - For Frontend hosting 

* API Gateway - To route traffic from the frontend to the microservices

## Kubernetes Architecture

This consist of the following:

* Control plane - managed by AWS

* Managed Node pool - these form the worker nodes, and deployed acrosss 3 availability zones to achieve high availability.
Furthermore, a `Node Autoscaler` is used to automatically scale out or in the number of active nodes using `CPU` as the metrics.

* Workloads are isolated using`namespaces` for each microservice, monitoring stack, ArgoCD, Cert-manager, and Traefik

* `Traefik` is used as Gateway Controller within the cluster. Service is of type `LoadBalancer`.

* Other routing components include - Gateway resource, HTTPRoute, and the service attached to the pods for the microservices

* Cert-Manager is used to achieve `TLS` connection from outside the cluster. This creates certificates through ClusterIssuer, which then creates
Secrets that the Gateway resource references. This gives an extra layer of security

* The microservices have services that are of type `ClusterIP`

* Observability is achieved with the use of:

Prometheus/ Grafana for monitoring (metrics collection)

EFK Stack for logging

Jaeger for tracing

## Application Deployment

This is with the use of `GitOps`

The pipelines for the deployment are in 2 parts: Continuous Integration and Continuous Delivery

* Continuous Integration - GitHub Actions is used to test, build and push the application Docker image to Amazon ECR.
The pipeline updates the Kubernetes manifest file for the microservices

* Continuous Delivery - ArgoCD, which runs within the same cluster detects the changes made to the Kubernetes manifest file(s) and 
synchronizes the changes with the cluster state.

## Goals 

* Horizontal scaling - This is achieved through the use of `Horizontal Pod Autoscaler`, which scales the number of pods for the
microservices based on `latency` and `requests per second`

* Security is achieved with use of:

1. API Gateway to access the cluster loadbalancer

2. An application load balancer between the API Gateway and the cluster

3. Service of Type `ClusterIP` for the microservices

4. The use of certificate manager for keeping communications secure

5. The use of network policies

6. The use of non-root access to file systems

7. The use of Role Based Access Control - RBAC with service accounts, cluster roles, and cluster role binding

* High Availability - Achieved through the use of Multi-Availability zones deployment

* Ingress/ Egress - This is achieved with the use of:

1. Traefik controller

2. Gateway - TLS termination occurs here

3. HTTPRoutes

4. Load Balancer

* Deployments - Infrastructures are created with the use of Terraform and application deployment through GitOps

* Maintainability - To achieve this:

1. Infrastructure as Code is used to create resources

2. Workloads are isolated using Namespaces

* Monitoring and Observabilty - Is achieved with the use of:

1. Prometheus and Grafana - for metrics monitoring (CPU, memory, latency, requests count, error rates, etc)

2. EFK stack - for logging

3. Jaeger - for tracing traffic flow among microservices

* Cost effectiveness - To achieve this, the following have been used:

1. Node autoscaler

2. OpenSource tools for observability

### Assumptions

* Organization seeks to reduce operational overhead by leveraging a managed service to run Kubernetes workload

* Organization wants to leverage OpenSource tools for observability for a start and migrate to managed services or SaaS application
later to separate observability from the main workloads

### Future Enhancement

* To integrate service mesh to provide extra layer of security


#####################################################################

## Architectural Diagram

![Architecture](./architecture/Kubernetes-Architecture.png)

