
# Identity and RBAC

## 1. Objective

Apply least-privilege access to Azure resources and Blob data
using Microsoft Entra ID and Azure Role-Based Access Control.

## 2. Virtual Machine Identity

The virtual machine `vm-web-01` uses a system-assigned Managed Identity.

This identity is tied to the lifecycle of the VM and allows it
to authenticate to supported Azure services without storing
credentials in scripts.

## 3. Storage Data Access

The VM identity has the `Storage Blob Data Reader` role assigned
at the Storage Account scope.

This role is intended to allow reading Blob data without granting
write or delete permissions.

A temporary `Storage Blob Data Contributor` assignment was used
during earlier upload and delete tests and was subsequently removed.

## 4. Administrative Access

The lab owner's account has the `Owner` role inherited from the
subscription.

The owner's `Storage Blob Data Contributor` assignment at the
Storage Account scope was removed during the least-privilege review.

Administrative permissions and Blob data permissions are separate:
the ability to manage Azure resources does not automatically imply
data-plane access to Blob contents.

## 5. Validation Results

The latest test using the VM Managed Identity returned:

| Operation | Result |
|---|---|
| List container | HTTP 404 |
| Upload Blob | HTTP 403 |
| Delete Blob | HTTP 403 |

The write and delete denials are consistent with the removal of
the Contributor role.

The HTTP 404 response does not conclusively establish whether
Blob read access works. A follow-up test against a known existing
Blob is required.

## 6. Security Recommendations

- Grant roles at the narrowest practical scope.
- Avoid permanent write permissions when read access is sufficient.
- Remove temporary role assignments after testing.
- Use Managed Identity instead of embedded credentials.
- Review inherited permissions at subscription and resource-group scope.
- Verify both allowed and denied operations during security testing.
