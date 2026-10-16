
# Azure Secure Web Lab

## 1. Project Overview

This project demonstrates the implementation and validation
of security controls for an Azure-hosted web workload and
its associated data storage.

The lab focuses on private networking, identity-based access,
data protection, security logging, alerting, and cost awareness.

## 2. Objectives

- Restrict public access to Azure Storage.
- Access Blob Storage through a Private Endpoint.
- Use Managed Identity instead of embedded credentials.
- Apply Azure RBAC to control data access.
- Enable Blob versioning and soft delete.
- Collect storage diagnostic logs in Log Analytics.
- Create an alert for repeated HTTP 403 responses.
- Validate security controls and document test results.

## 3. Architecture

The lab includes:

- Azure Virtual Network and subnets
- Windows Server virtual machine running IIS
- Network Security Group
- Azure Storage Account
- Private Endpoint and Private DNS
- Azure Key Vault
- Managed Identity and Azure RBAC
- Log Analytics Workspace
- Azure Monitor log search alert

The Application Gateway was deployed during an earlier
phase and subsequently removed to reduce costs.

## 4. Network Security

- Azure Storage public network access is disabled.
- Blob Storage is accessed through a Private Endpoint.
- Private DNS resolves the Blob endpoint to the private IP
  address `10.0.3.4` from the VM.
- HTTPS connectivity on port 443 was validated from the VM.
- The web subnet is protected by an NSG.

## 5. Identity and Access Management

The VM uses a system-assigned Managed Identity.

Confirmed configuration:
- Storage Blob Data Reader assigned to the VM identity.
- Temporary Storage Blob Data Contributor access was removed.
- The VM identity successfully accessed a Key Vault secret
  during an earlier validation.

The final Storage RBAC validation remains incomplete:
the latest container-list request returned HTTP 404,
while upload and delete requests returned HTTP 403.

These results require further investigation before claiming
that all access controls have been fully validated.

## 6. Data Protection

The Storage Account is configured with:

- Secure transfer required
- Minimum TLS version 1.2
- Anonymous Blob access disabled
- Blob versioning enabled
- Blob soft delete enabled
- Container soft delete enabled

Multiple blob versions were observed during testing.

A successful restoration after soft deletion was not confirmed.

## 7. Monitoring and Alerting

Storage diagnostic logs are sent to Log Analytics.

Workspace: `law-secure-web-lab`

A log search alert was configured to detect repeated HTTP 403
responses in `StorageBlobLogs`.

Alert rule: `alert-blob-403-failures`

The rule fired when four matching 403 records were detected
against a configured threshold of three.

The investigated requests originated from the lab owner's
public IP while attempting to access the Storage container
from a personal device. The requests were denied.

No external notification Action Group was configured.

## 8. Cost Management

The VM was stopped and the Application Gateway and an
associated public IP resource were removed to reduce costs.

These figures are historical portal estimates and may change
as billing data is updated.

## 9. Known Limitations

- Application Gateway is no longer deployed.
- SQL Server was not deployed because of regional capacity
  constraints.
- Successful restoration from soft delete was not verified.
- Final Storage RBAC read/write/delete validation is pending.
- External alert notifications are not configured.

## 10. Lessons Learned

- Private Endpoints require correct private DNS resolution.
- Managed Identity avoids embedding credentials in scripts.
- Azure control-plane permissions differ from data-plane roles.
- HTTP 403 and 404 responses must be interpreted in context.
- Diagnostic logs and alerts help investigate access failures.
- Stopping a VM does not eliminate all associated resource costs.


