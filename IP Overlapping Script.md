**************************************************************
# SOP: Creating Dedicated Service Account and Kubeconfig for IP Overlapping Script

## Objective
To replace the usage of the root/admin kubeconfig in the IP Overlapping monitoring script by creating a dedicated Service Account with a long-lived token and generating a separate kubeconfig for secure cluster access.

---

## Prerequisites
- Access to Rancher UI
- Cluster administrator privileges
- kubectl installed on the management workstation/server

---

## Step 1: Create a Service Account

Create a dedicated Service Account in the required namespace.

kubectl create serviceaccount ip-overlap-sa -n default

Verify:

kubectl get sa -n default

---

## Step 2: Assign Required Permissions

Create a ClusterRoleBinding to provide the necessary permissions.

kubectl create clusterrolebinding ip-overlap-sa-binding \
  --clusterrole=cluster-admin \
  --serviceaccount=default:ip-overlap-sa

Verify:

kubectl get clusterrolebinding | grep ip-overlap-sa

---

## Step 3: Generate Long-Lived Token

Generate a token for the Service Account.

kubectl create token ip-overlap-sa -n default --duration=87600h

Copy and save the generated token securely.

---

## Step 4: Download Existing Kubeconfig from Rancher

1. Login to Rancher.
2. Open the target cluster.
3. Click Kubeconfig.
4. Download or copy the cluster kubeconfig.

---

## Step 5: Create New Dedicated Kubeconfig

Create a new file:

vi ip_kubeconfig.yaml

Paste the downloaded Rancher kubeconfig.

Modify the following fields:

### Update Server URL

Replace Rancher server URL with the target cluster API endpoint if required.

Example:

server: https://<cluster-api-endpoint>:6443

### Update User Token

Replace the existing token with the Service Account token generated in Step 3.

Example:

users:
- name: ip-overlap-sa
  user:
    token: <generated-token>

### Update Context Name

contexts:
- context:
    cluster: cluster-name
    user: ip-overlap-sa
  name: ip-overlap-context

Set current context:

current-context: ip-overlap-context

---

## Step 6: Validate New Kubeconfig

Test cluster connectivity.

export KUBECONFIG=ip_kubeconfig.yaml
kubectl get nodes

Verify namespace access:

kubectl get pods -A

---

## Step 7: Update IP Overlapping Script

Modify the script to use the newly created kubeconfig.

Example:

export KUBECONFIG=/home/esbuser/ip_kubeconfig.yaml

Or update the script path accordingly.

---

## Step 8: Execute and Validate

Run the IP Overlapping script manually.

./ip-overlapping.sh

Verify:
- Script executes successfully.
- Cluster resources are accessible.
- No dependency on root/admin kubeconfig remains.

---

## Result

The IP Overlapping script now uses a dedicated Service Account-based kubeconfig with a long-lived token, eliminating dependency on the root/admin kubeconfig and improving security and access management.
