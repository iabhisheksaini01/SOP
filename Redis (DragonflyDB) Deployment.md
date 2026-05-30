#####################################
# SOP – Redis (DragonflyDB) Deployment and Validation using Jenkins

## Document Information

| Field             | Value                                         |
| ----------------- | --------------------------------------------- |
| SOP Name          | Redis (DragonflyDB) Deployment and Validation |
| Version           | 1.0                                           |
| Deployment Method | Jenkins Pipeline                              |
| Repository        | ATLAF Redis Repository                        |
| Owner Team        | Platform / DevOps Team                        |

---

# Purpose

This SOP describes the standardized process for deploying and validating Redis (DragonflyDB) instances on Kubernetes clusters using the automated Jenkins deployment pipeline.

---

# References

### Jenkins Pipeline

http://172.23.12.160:8082/job/Infra-automation/job/Development/job/Redis-deploy-nonpv-pipeline-prod/

### Deployment Template

https://bitbucket.airtel.africa/bitbucket/projects/ATLAF/repos/redis/browse/REDIS_Automation/redis-template.yaml

### Jenkinsfile

https://bitbucket.airtel.africa/bitbucket/projects/ATLAF/repos/redis/browse/REDIS_Automation/jenkinsfile

---

# Supported OPCOs

The Jenkins deployment pipeline currently supports deployment to the following OPCO Kubernetes clusters:

* cd
* cg
* ga
* ke
* mg
* mw
* ne
* ng
* rw
* sc
* td
* tz
* ug
* ngpsb
* zm
* ug-54

**Note:**
The target cluster is selected automatically based on the OPCO_NAME parameter provided during Jenkins job execution.

---

# Assumptions

* Dragonfly Operator is already installed and operational in the target Kubernetes cluster.
* Jenkins server has network connectivity to all supported OPCO clusters.
* Required deployment permissions are available for the Jenkins execution user.
* Access to the Redis repository is available during deployment execution.

---

# Prerequisites

* Jenkins access
* Bitbucket repository access
* Connectivity to target Kubernetes cluster
* Required deployment permissions
* Dragonfly Operator available in target cluster

---

# Deployment Overview

The Jenkins pipeline automates the complete deployment lifecycle by:

* Cloning the Redis repository
* Generating deployment manifests from template files
* Validating generated YAML
* Identifying the target OPCO cluster
* Creating namespace if not available
* Deploying Redis (DragonflyDB)
* Performing deployment validation

---

# Deployment Procedure

## Step 1 – Launch Jenkins Job

1. Open the Jenkins Redis deployment pipeline.
2. Click **Build with Parameters**.

---

## Step 2 – Provide Deployment Parameters

Provide the required deployment values:

| Parameter      | Description                            |
| -------------- | -------------------------------------- |
| NAMESPACE      | Namespace where Redis will be deployed |
| CPU_REQUEST    | CPU request for Redis pods             |
| MEMORY_REQUEST | Memory request for Redis pods          |
| CPU_LIMIT      | CPU limit for Redis pods               |
| MEMORY_LIMIT   | Memory limit for Redis pods            |
| OPCO_NAME      | Target OPCO cluster                    |

### Example

| Parameter      | Sample Value |
| -------------- | ------------ |
| NAMESPACE      | redis-prod   |
| CPU_REQUEST    | 100m         |
| MEMORY_REQUEST | 1Gi          |
| CPU_LIMIT      | 1500m        |
| MEMORY_LIMIT   | 1Gi          |
| OPCO_NAME      | ug           |

---

## Step 3 – Repository Checkout

The pipeline clones the Redis repository from Bitbucket and retrieves all required deployment artifacts.

---

## Step 4 – Manifest Generation

The deployment template file is processed using the user-provided parameters.

The following values are dynamically substituted:

* Namespace
* CPU Request
* Memory Request
* CPU Limit
* Memory Limit

Generated output file:

```text
redis_final.yml
```

---

## Step 5 – YAML Validation

The generated manifest is validated to ensure:

* No unresolved variables remain.
* YAML syntax is valid.
* Deployment parameters are substituted successfully.

If unresolved variables are detected, the deployment process stops automatically.

---

## Step 6 – Target Cluster Selection

Based on the selected OPCO, Jenkins maps the deployment request to the corresponding Kubernetes cluster.

Cluster connectivity is validated before deployment execution.

---

## Step 7 – Rancher Project Identification

The pipeline retrieves the Rancher Project ID associated with the target cluster.

This Project ID is used while creating namespaces to ensure proper Rancher project mapping.

---

## Step 8 – Namespace Validation

The pipeline verifies whether the specified namespace already exists.

### If Namespace Exists

Deployment proceeds to the next stage.

### If Namespace Does Not Exist

The pipeline automatically:

* Creates the namespace
* Associates the namespace with the correct Rancher project
* Continues deployment

---

## Step 9 – Redis Deployment

The generated deployment manifest is copied to the target cluster.

Deployment is performed using Kubernetes apply operations.

This step creates the DragonflyDB resources in the specified namespace.

---

## Step 10 – Deployment Completion

Jenkins validates successful execution of all deployment stages and marks the job as successful.

---

# Validation Procedure

## Verify Namespace

```bash
kubectl get ns <namespace>
```

---

## Verify Dragonfly Resource

```bash
kubectl get dragonfly -n <namespace>
```

Expected Result:

```text
Resource should be present and healthy.
```

---

## Verify Pods

```bash
kubectl get pods -n <namespace>
```

Expected Result:

```text
All pods should be in Running state.
```

---

## Verify Services

```bash
kubectl get svc -n <namespace>
```

---

## Verify Events

```bash
kubectl get events -n <namespace>
```

---

## Verify Resource Utilization

```bash
kubectl top pods -n <namespace>
```

Validate:

* CPU utilization
* Memory utilization
* Pod stability

---

## Verify Redis Connectivity

Connect to a Redis pod:

```bash
kubectl exec -it <pod-name> -n <namespace> -- redis-cli
```

Run:

```bash
PING
```

Expected Output:

```text
PONG
```

---

# Troubleshooting

## Deployment Failure

Review Jenkins console logs and identify the failed pipeline stage.

---

## YAML Validation Failure

Verify that all deployment parameters were provided correctly and no unresolved variables remain in the generated manifest.

---

## Namespace Creation Failure

Verify:

```bash
kubectl get ns
```

Check deployment permissions and cluster connectivity.

---

## Pod Startup Failure

```bash
kubectl describe pod <pod-name> -n <namespace>
```

```bash
kubectl logs <pod-name> -n <namespace>
```

Review pod events, scheduling issues, image pull errors, and application logs.

---

## Resource Constraint Issues

Verify the provided CPU and Memory values.

Check:

```bash
kubectl top pods -n <namespace>
```

---

# Rollback Procedure

Delete the deployed Dragonfly resource:

```bash
kubectl delete dragonfly redis -n <namespace>
```

Verify cleanup:

```bash
kubectl get pods -n <namespace>
```

---

# Success Criteria

Deployment is considered successful when:

* Jenkins pipeline completes successfully.
* Namespace exists and is accessible.
* Dragonfly resource is created successfully.
* All Redis pods are in Running state.
* Services are created successfully.
* Redis connectivity validation returns PONG.
* No critical errors are observed in pod events or logs.
* Resource utilization remains within configured limits.
