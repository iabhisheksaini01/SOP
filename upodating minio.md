**************************************************************
# SOP: Upgrading and Replacing a Binary on Remote Server Using SCP

## Objective
To securely transfer and replace an existing binary on a remote server using SCP, ensuring backup, correct placement, and proper permissions in a generic and reusable manner.

---

## Prerequisites
- SSH access to source and target server
- SCP access enabled
- Required user privileges (sudo/root)
- New binary file available on source server

---

## Step 1: Verify Existing Binary

Login to target server:

ssh -p <PORT> <user>@<target-server>

Check current version (if applicable):

<binary-name> --version

Identify binary location:

which <binary-name>

---

## Step 2: Take Backup of Existing Binary

Always take a backup before replacement:

sudo cp <existing-binary-path> <existing-binary-path>.bak

Verify backup:

ls -l <existing-binary-path>*

---

## Step 3: Transfer New Binary

From source server, transfer the file:

scp -P <PORT> <binary-name> <user>@<target-server>:/tmp/

---

## Step 4: Verify Transfer

Login to target server and confirm file exists:

ls -l /tmp/<binary-name>

---

## Step 5: Replace Existing Binary

Move new binary to the correct location:

sudo mv /tmp/<binary-name> <target-binary-path>

Set executable permission:

sudo chmod +x <target-binary-path>

---

## Step 6: Validate Installation

Check updated version:

<binary-name> --version

Verify binary path:

which <binary-name>

---

## Step 7: Rollback (If Required)

Restore backup if needed:

sudo mv <target-binary-path>.bak <target-binary-path>

Validate rollback:

<binary-name> --version

---

## Step 8: Troubleshooting

### Issue: Host key verification failed
Run:

ssh-keygen -R "[<target-server>]:<PORT>"

---

### Issue: File not found in /tmp
Re-transfer file using SCP.

---

### Issue: Incorrect binary still in use
Check priority path:

which <binary-name>

Ensure correct path is replaced.

---

## Examples

### Example 1: MC (MinIO Client) Upgrade

- Binary Name: mc  
- Source Server: k8s-master  
- Target Server: 172.26.38.126  
- Port: 922  
- User: esbuser  
- Target Path: /usr/local/bin/mc  

#### Transfer:
scp -P 922 mc esbuser@172.26.38.126:/tmp/

#### Backup:
sudo cp /usr/local/bin/mc /usr/local/bin/mc.bak

#### Replace:
sudo mv /tmp/mc /usr/local/bin/mc  
sudo chmod +x /usr/local/bin/mc  

#### Validate:
mc --version  
which mc  

---

### Example 2: kubectl Binary Upgrade

- Binary Name: kubectl  
- Target Path: /usr/local/bin/kubectl  

#### Transfer:
scp -P 922 kubectl user@server:/tmp/

#### Backup:
sudo cp /usr/local/bin/kubectl /usr/local/bin/kubectl.bak  

#### Replace:
sudo mv /tmp/kubectl /usr/local/bin/kubectl  
sudo chmod +x /usr/local/bin/kubectl  

#### Verify:
kubectl version --client  

---

## Result

The binary has been successfully upgraded with:
- Backup created before modification
- Secure transfer using SCP
- Correct binary path replacement
- Proper execution permissions
- Rollback mechanism available for safety

**************************************************************
