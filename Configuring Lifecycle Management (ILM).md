**************************************************************
# SOP: Configuring Lifecycle Management (ILM) for MinIO Buckets

## Objective
To configure and manage object lifecycle policies (ILM) for MinIO buckets to automate cleanup of old or unused data for both versioned and non-versioned buckets.

---

## Prerequisites
- MinIO client (mc) installed and configured
- Proper access to MinIO server
- Required permissions to modify bucket lifecycle policies
- Valid alias configured in mc

---

## Step 1: Verify MinIO Alias Configuration

Check available aliases:

mc alias ls

Ensure the required alias exists and is correctly configured.

---

## Step 2: Check Bucket Versioning Status

Verify whether bucket versioning is enabled:

mc version info <mc_alias>/<bucket_name>

Example output:
- Versioning enabled
- Versioning suspended
- Versioning not enabled

---

## Step 3: Check Existing Lifecycle Policies

Check current ILM rules:

mc ilm ls <mc_alias>/<bucket_name>

If no policy exists, you may see:
mc: <ERROR> Unable to get lifecycle. The lifecycle configuration does not exist.

---

## Step 4: Configure Lifecycle Policy (Versioned Bucket)

For versioned buckets, configure rules for:

### 4.1 Expire Non-Current Versions

Delete older object versions after specified days:

mc ilm rule add <mc_alias>/<bucket_name> --noncurrent-expire-days <days>

---

### 4.2 Remove Delete Markers

Automatically remove delete markers:

mc ilm rule add <mc_alias>/<bucket_name> --expire-delete-marker

---

## Step 5: Configure Lifecycle Policy (Non-Versioned Bucket)

For non-versioned buckets:

mc ilm add <mc_alias>/<bucket_name> --expire-days <days>

---

## Step 6: Validate Lifecycle Configuration

Check configured rules:

mc ilm ls <mc_alias>/<bucket_name>

Ensure:
- Rules are in Enabled state
- Correct expiration values are applied

---

## Step 7: Monitor Bucket Size

Check bucket usage including versions:

mc du --versions --recursive <mc_alias>/<bucket_name>

---

## Step 8: Troubleshooting

### Issue: Lifecycle not found
- No ILM rules configured  
- Solution: Add lifecycle rules using `mc ilm rule add` or `mc ilm add`

---

### Issue: Cleanup not happening
- Verify rule status is Enabled
- Check number of days configured

---

### Issue: Incorrect bucket type assumption
- Confirm versioning status before applying ILM rules

---

## Examples

### Example 1: Versioned Bucket Cleanup

- Alias: localadmin  
- Bucket: canvas-prod  

#### Check Versioning:
mc version info localadmin/canvas-prod

Output:
versioning is enabled

---

#### Add Cleanup Rules:

Expire old versions after 7 days:
mc ilm rule add localadmin/canvas-prod --noncurrent-expire-days 7

Remove delete markers:
mc ilm rule add localadmin/canvas-prod --expire-delete-marker

---

#### Validate:
mc ilm ls localadmin/canvas-prod

Expected:
- Noncurrent versions expire after 7 days
- Delete markers auto-cleaned

---

### Example 2: Non-Versioned Bucket Cleanup

#### Add rule:
mc ilm add myminio/sample-bucket --expire-days 30

---

#### Validate:
mc ilm ls myminio/sample-bucket

---

### Example 3: Storage Usage Check

mc du --versions --recursive localadmin/canvas-prod

---

## Result

The lifecycle policies are successfully configured, ensuring:

- Automated cleanup of old objects
- Efficient storage utilization
- Support for both versioned and non-versioned buckets
- Reduced manual maintenance effort
- Improved data lifecycle management

**************************************************************
