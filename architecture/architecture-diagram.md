
# Azure Secure Web Lab — Architecture

```mermaid
flowchart TB
    subgraph VNET["Azure Virtual Network: 10.0.0.0/16"]
        subgraph WEB["Web Subnet: 10.0.2.0/24"]
            VM["Windows Server VM<br/>vm-web-01<br/>IIS + Managed Identity"]
            NSG["Network Security Group<br/>nsg-web"]
        end

        subgraph PE["Private Endpoint Subnet: 10.0.3.0/24"]
            STORAGEPE["Storage Private Endpoint<br/>10.0.3.4"]
        end

        DNS["Private DNS Zone<br/>privatelink.blob.core.windows.net"]
    end

    STORAGE["Storage Account<br/>stsecweblab<br/>Public Access Disabled"]

    VM -->|"HTTPS / TCP 443"| STORAGEPE
    STORAGEPE --> STORAGE
    DNS -.->|"Private name resolution"| STORAGEPE

    VM -->|"Diagnostic and test activity"| MON["Log Analytics<br/>law-secure-web-lab"]
    MON --> ALERT["Azure Monitor Alert<br/>403 failures"]

    KV["Key Vault<br/>kv-secure-web-lab"]
    VM -.->|"Managed Identity"| KV

    classDef private fill:#dbeafe,stroke:#2563eb,color:#111827
    classDef monitoring fill:#dcfce7,stroke:#16a34a,color:#111827
    classDef removed fill:#fee2e2,stroke:#dc2626,color:#111827

    class VM,STORAGEPE,STORAGE,DNS,KV private
    class MON,ALERT monitoring
```

## Notes

- Storage public network access is disabled.
- The VM accesses Blob Storage through a Private Endpoint.
- Private DNS resolves the Blob endpoint to the private IP.
- Managed Identity is used for Azure resource access.
- Storage diagnostics are sent to Log Analytics.
- The Application Gateway was removed to reduce costs.
- The SQL Server component was not deployed.
