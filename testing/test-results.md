
# Security Test Results

## 1. Private Endpoint Connectivity

**Objective:** Verify private connectivity between the VM and Azure Blob Storage.

**Tests performed:**
- DNS resolution from the VM.
- TCP connectivity to the Blob endpoint on port 443.
- Verification that the Blob hostname resolves to private IP `10.0.3.4`.

**Result:** Passed in the earlier connectivity test.

## 2. Public Network Access

**Objective:** Verify that public access to the Storage Account is disabled.

**Configuration:** Public network access is disabled.

**Result:** Configuration verified in Azure Portal. An external access attempt returned HTTP 403.

## 3. Managed Identity

**Objective:** Access Azure resources without embedding credentials.

**Test performed:** The VM's system-assigned Managed Identity retrieved a Key Vault secret using the Azure Instance Metadata Service and REST API.

**Result:** Passed in the earlier test.

## 4. Blob Versioning

**Objective:** Verify that Blob versioning preserves previous file versions.

**Test performed:** A test Blob was uploaded more than once, and multiple version IDs were observed.

**Result:** Passed.

## 5. Soft Delete

**Objective:** Verify recovery behavior after deleting a Blob.

**Test performed:** A Blob was deleted and appeared in the deleted-Blob listing.

**Result:** Partial. The undelete request returned HTTP 200, but subsequent retrieval returned HTTP 404. Successful restoration was not confirmed.

## 6. Storage RBAC

**Objective:** Validate least-privilege access for the VM Managed Identity.

**Configuration:** Storage Blob Data Reader remains assigned to the VM identity. The temporary Storage Blob Data Contributor assignment was removed.

**Latest test results:**
- Container listing: HTTP 404.
- Upload: HTTP 403.
- Delete: HTTP 403.

**Result:** Inconclusive. The 404 response does not establish whether read access works. Further testing against a known existing Blob is required.

## 7. Diagnostic Logging and Alerting

**Objective:** Detect repeated failed Blob requests.

**Test performed:** Storage diagnostic logs were queried in Log Analytics. The alert rule `alert-blob-403-failures` fired after four matching HTTP 403 records were detected against a threshold of three.

**Result:** Passed for alert triggering.

**Investigation note:** The requests were correlated with the lab owner's attempt to access the container from a personal device while public network access was disabled. The requests were denied.

## 8. Cost Management

**Observation date:** October 15, 2026.

- Accumulated actual cost shown: USD 21.55.
- Forecast for October: USD 52.52.
- The VM was stopped.
- The Application Gateway and an associated public IP resource were removed.

These figures are portal observations and may change as billing data is updated.

## 9. Overall Assessment

The lab demonstrates private storage connectivity, identity-based access, Blob versioning, diagnostic logging, alerting, and cost management.

The final RBAC read test and successful recovery from soft delete remain unverified.
