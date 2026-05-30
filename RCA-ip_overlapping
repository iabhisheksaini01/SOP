# RCA – IP Overlap Automation Authentication and Reliability Improvement

## Summary

During a review of the IP Overlap Validation and Remediation automation, it was identified that the script was dependent on a kubeconfig containing an expiring authentication token. Since the script is executed as a scheduled cron job and requires continuous Kubernetes API access, token expiry could lead to authentication failures and interruption of automated cluster health checks.

Additionally, the script and supporting files were found to be stored under the `/tmp` directory, creating a risk of accidental deletion and automation failure.

---

## Background

The IP Overlap automation script is responsible for:

* Collecting Pod and Service IP information across Kubernetes namespaces.
* Detecting duplicate Pod IPs.
* Detecting duplicate Service IPs.
* Detecting Pod-to-Service IP conflicts.
* Automatically restarting affected Pods to remediate overlap issues.
* Generating execution logs and CSV reports for operational tracking.

The script runs as a scheduled cron job from the cluster server and requires authenticated access to the Kubernetes API server through a kubeconfig file.

---

## Issue Identified

During validation of the automation setup, the following issues were identified:

### 1. Authentication Dependency

The script was using a kubeconfig containing an expiring token. Once the token expired, the script would no longer be able to authenticate with the Kubernetes API server, causing scheduled executions to fail.

### 2. Non-Persistent Script Location

The script and related files were stored under the `/tmp` directory. Since `/tmp` is a temporary filesystem location, files can be removed during system cleanup, maintenance activities, or server reboots.

---

## Root Cause

### Authentication Risk

The automation depended on a kubeconfig configured with a short-lived token. Since the script executes through cron and operates without manual intervention, token expiration created a single point of failure for the automation.

### Script Storage Risk

Operational scripts were stored in a temporary directory instead of a persistent application location, increasing the risk of accidental deletion and loss of automation functionality.

---

## Impact

If not addressed, the following issues could occur:

* Failure of scheduled IP overlap validation jobs.
* Failure of automated remediation activities.
* Inability to retrieve Pod and Service IP information from the cluster.
* Loss of automated reporting and log generation.
* Increased manual operational effort and troubleshooting.
* Delayed detection and resolution of IP overlap issues.
* Automation failure due to accidental deletion of scripts from the `/tmp` directory.

---

## Resolution Implemented

### Authentication Improvement

* Created a dedicated Kubernetes ServiceAccount for the automation.
* Generated a dedicated kubeconfig using the ServiceAccount credentials.
* Updated the IP Overlap automation script to use the dedicated kubeconfig.
* Validated successful authentication and execution across the required production OPCOs.

### Script Reliability Improvement

* Identified automation files stored in the `/tmp` directory.
* Moved scripts and supporting files to the application directory.
* Updated execution paths where required.
* Validated successful execution after relocation.

---

## Validation Performed

* Verified Kubernetes API connectivity using the new ServiceAccount-based kubeconfig.
* Executed the IP overlap automation script successfully.
* Confirmed detection and remediation logic execution.
* Verified log and CSV report generation.
* Validated execution across multiple production OPCOs.

---

## Benefits Achieved

* Improved reliability of scheduled automation jobs.
* Reduced dependency on expiring authentication credentials.
* Ensured uninterrupted Kubernetes API access for automation tasks.
* Improved operational stability of the IP overlap remediation process.
* Eliminated risks associated with storing operational scripts in temporary locations.
* Improved maintainability and supportability of the automation solution.

---

## Preventive Actions

* Use dedicated ServiceAccounts for automation workloads requiring Kubernetes API access.
* Store operational scripts in persistent application directories instead of temporary locations.
* Periodically review automation authentication mechanisms and access controls.
* Maintain proper backup and ownership of production automation scripts.
* Validate automation dependencies during periodic operational reviews.
