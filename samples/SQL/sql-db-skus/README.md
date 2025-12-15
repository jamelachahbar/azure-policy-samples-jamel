# Allowed SQL DB SKUs

This policy enables you to specify a set of SQL DB SKUs

## Try on Portal

[![Deploy to Azure](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FSQL%2Fsql-db-skus%2Fazurepolicy.json)

## Try with PowerShell

**Create the policy definition:**
````powershell
$definition = New-AzPolicyDefinition -Name "sql-db-skus" -DisplayName "Allowed SQL DB SKUs" -description "This policy enables you to specify a set of SQL DB SKUs" -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/SQL/sql-db-skus/azurepolicy.rules.json' -Parameter 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/SQL/sql-db-skus/azurepolicy.parameters.json' -Mode All
$definition
````

**Assign the policy with parameters:**
````powershell
# Example: Allow only Basic and Standard S0-S2 (see Sample Parameters section below for more examples)
$assignment = New-AzPolicyAssignment -Name <assignmentname> -Scope <scope> -PolicyDefinition $definition -PolicyParameter @{
    effect = "Deny"  # Use "Audit" to log non-compliance without blocking, "Disabled" to turn off
    listOfSKUName = @("Basic", "S0", "S1", "S2")
    listOfSKUId = @()
}
$assignment 
````



## Try with CLI

**Create the policy definition:**
````cli
az policy definition create --name 'sql-db-skus' --display-name 'Allowed SQL DB SKUs' --description 'This policy enables you to specify a set of SQL DB SKUs' --rules 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/SQL/sql-db-skus/azurepolicy.rules.json' --params 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/SQL/sql-db-skus/azurepolicy.parameters.json' --mode All
````

**Assign the policy with parameters (Example 1: Basic and Standard S0-S2):**
````cli
az policy assignment create --name 'sql-db-skus-assignment' --scope '<scope>' --policy "sql-db-skus" --params "{'effect':{'value':'Deny'}, 'listOfSKUName':{'value':['Basic','S0','S1','S2']}, 'listOfSKUId':{'value':[]}}"
````

**Update an existing assignment with new parameters:**
````cli
# Example: Change to allow DTU Standard + vCore General Purpose (Example 8)
az policy assignment create --name 'sql-db-skus-assignment' --scope '<scope>' --policy "sql-db-skus" --params "{'effect':{'value':'Deny'}, 'listOfSKUName':{'value':['S0','S1','S2','S3','GP_Gen5_2','GP_Gen5_4','GP_Gen5_8']}, 'listOfSKUId':{'value':[]}}"
````

**Common scope examples:**
````cli
# Subscription scope
--scope "/subscriptions/{subscription-id}"

# Resource group scope
--scope "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}"

# Management group scope
--scope "/providers/Microsoft.Management/managementGroups/{management-group-id}"
````

## Sample Parameters

> **Note:** This policy applies to **Azure SQL Database** only (resource type: `Microsoft.SQL/servers/databases`). It does not apply to Azure SQL Managed Instance, SQL Server on Azure VMs, or Azure Synapse Analytics.

### Common SQL Database SKU Names

Azure SQL Database offers two purchasing models with different service tiers:

#### DTU-Based Purchasing Model

**Basic Tier** - Development and testing workloads
- `Basic` (5 DTUs, 2 GB storage)

**Standard Tier** - Most business workloads
- `S0` (10 DTUs), `S1` (20 DTUs), `S2` (50 DTUs), `S3` (100 DTUs)
- `S4` (200 DTUs), `S6` (400 DTUs), `S7` (800 DTUs), `S9` (1600 DTUs), `S12` (3000 DTUs)

**Premium Tier** - High-performance OLTP applications
- `P1` (125 DTUs), `P2` (250 DTUs), `P4` (500 DTUs), `P6` (1000 DTUs)
- `P11` (1750 DTUs), `P15` (4000 DTUs)

#### vCore-Based Purchasing Model

**General Purpose** - Budget-oriented balanced compute and storage (2-128 vCores)
- Gen5: `GP_Gen5_2`, `GP_Gen5_4`, `GP_Gen5_6`, `GP_Gen5_8`, `GP_Gen5_10`, `GP_Gen5_12`, `GP_Gen5_14`, `GP_Gen5_16`, `GP_Gen5_18`, `GP_Gen5_20`, `GP_Gen5_24`, `GP_Gen5_32`, `GP_Gen5_40`, `GP_Gen5_80`, `GP_Gen5_128`
- Serverless Gen5: `GP_S_Gen5_1`, `GP_S_Gen5_2`, `GP_S_Gen5_4`, `GP_S_Gen5_6`, `GP_S_Gen5_8`, etc.

**Business Critical** - Low latency, high IOPS, multiple replicas (2-128 vCores)
- Gen5: `BC_Gen5_2`, `BC_Gen5_4`, `BC_Gen5_6`, `BC_Gen5_8`, `BC_Gen5_10`, `BC_Gen5_12`, `BC_Gen5_14`, `BC_Gen5_16`, `BC_Gen5_18`, `BC_Gen5_20`, `BC_Gen5_24`, `BC_Gen5_32`, `BC_Gen5_40`, `BC_Gen5_80`, `BC_Gen5_128`

**Hyperscale** - Highly scalable storage and compute (2-128 vCores)
- Gen5: `HS_Gen5_2`, `HS_Gen5_4`, `HS_Gen5_6`, `HS_Gen5_8`, `HS_Gen5_10`, `HS_Gen5_12`, `HS_Gen5_14`, `HS_Gen5_16`, `HS_Gen5_18`, `HS_Gen5_20`, `HS_Gen5_24`, `HS_Gen5_32`, `HS_Gen5_40`, `HS_Gen5_80`

### Example 1: Restrict to cost-effective DTU tiers (Basic and lower Standard)

Use case: Development and test environments

```json
{
    "effect": {
        "value": "Deny"
    },
    "listOfSKUName": {
        "value": ["Basic", "S0", "S1", "S2"]
    },
    "listOfSKUId": {
        "value": []
    }
}
```

### Example 2: Allow Standard tier only (all sizes)

Use case: Production workloads with controlled costs

```json
{
    "effect": {
        "value": "Deny"
    },
    "listOfSKUName": {
        "value": ["S0", "S1", "S2", "S3", "S4", "S6", "S7", "S9", "S12"]
    },
    "listOfSKUId": {
        "value": []
    }
}
```

### Example 3: Allow Premium tier for high-performance workloads

Use case: Mission-critical OLTP applications requiring low latency

```json
{
    "effect": {
        "value": "Deny"
    },
    "listOfSKUName": {
        "value": ["P1", "P2", "P4", "P6", "P11", "P15"]
    },
    "listOfSKUId": {
        "value": []
    }
}
```

### Example 4: Allow General Purpose vCore-based (2-16 vCores)

Use case: Modern applications using vCore model with controlled compute sizes

```json
{
    "effect": {
        "value": "Deny"
    },
    "listOfSKUName": {
        "value": [
            "GP_Gen5_2", "GP_Gen5_4", "GP_Gen5_6", "GP_Gen5_8", 
            "GP_Gen5_10", "GP_Gen5_12", "GP_Gen5_14", "GP_Gen5_16"
        ]
    },
    "listOfSKUId": {
        "value": []
    }
}
```

### Example 5: Allow both General Purpose and Business Critical (small to medium sizes)

Use case: Production workloads with flexibility between performance tiers

```json
{
    "effect": {
        "value": "Deny"
    },
    "listOfSKUName": {
        "value": [
            "GP_Gen5_2", "GP_Gen5_4", "GP_Gen5_8", 
            "BC_Gen5_2", "BC_Gen5_4", "BC_Gen5_8"
        ]
    },
    "listOfSKUId": {
        "value": []
    }
}
```

### Example 6: Allow Hyperscale tier for scalable workloads

Use case: Applications requiring rapid scaling and large storage capacity (10 GB - 100 TB)

```json
{
    "effect": {
        "value": "Deny"
    },
    "listOfSKUName": {
        "value": [
            "HS_Gen5_2", "HS_Gen5_4", "HS_Gen5_8", "HS_Gen5_16", 
            "HS_Gen5_24", "HS_Gen5_32", "HS_Gen5_40", "HS_Gen5_80"
        ]
    },
    "listOfSKUId": {
        "value": []
    }
}
```

### Example 7: Allow serverless compute for auto-scaling workloads

Use case: Intermittent or unpredictable usage patterns with auto-pause capability

```json
{
    "effect": {
        "value": "Deny"
    },
    "listOfSKUName": {
        "value": [
            "GP_S_Gen5_1", "GP_S_Gen5_2", "GP_S_Gen5_4", 
            "GP_S_Gen5_6", "GP_S_Gen5_8", "GP_S_Gen5_10"
        ]
    },
    "listOfSKUId": {
        "value": []
    }
}
```

### Example 8: Mixed environment (DTU Standard + vCore General Purpose)

Use case: Organization transitioning from DTU to vCore model

```json
{
    "effect": {
        "value": "Deny"
    },
    "listOfSKUName": {
        "value": [
            "S0", "S1", "S2", "S3",
            "GP_Gen5_2", "GP_Gen5_4", "GP_Gen5_8"
        ]
    },
    "listOfSKUId": {
        "value": []
    }
}
```

### Example 9: Using SKU IDs (advanced scenario)

Use case: When you need to specify exact SKU GUIDs for compliance reasons

```json
{
    "effect": {
        "value": "Deny"
    },
    "listOfSKUName": {
        "value": []
    },
    "listOfSKUId": {
        "value": [
            "7203483a-c4fb-4304-9e9f-17c71c904f5d",
            "f1173c43-91bd-4aaa-973c-54e79e15235b"
        ]
    }
}
```

### Example 10: Audit mode (report only, don't block)

Use case: Assess current SKU usage before enforcing restrictions

```json
{
    "effect": {
        "value": "Audit"
    },
    "listOfSKUName": {
        "value": ["Basic", "S0", "S1", "S2"]
    },
    "listOfSKUId": {
        "value": []
    }
}
```

### How to Apply Parameters

**Method 1: Using Azure CLI**
```bash
# Create or update assignment with specific parameters
az policy assignment create \
  --name 'sql-db-skus-assignment' \
  --scope '/subscriptions/{sub-id}' \
  --policy 'sql-db-skus' \
  --params "{'effect':{'value':'Deny'}, 'listOfSKUName':{'value':['S0','S1','S2']}, 'listOfSKUId':{'value':[]}}"
```

**Method 2: Using Azure PowerShell**
```powershell
$params = @{
    effect = "Deny"
    listOfSKUName = @("S0", "S1", "S2", "S3", "GP_Gen5_2", "GP_Gen5_4", "GP_Gen5_8")
    listOfSKUId = @()
}
New-AzPolicyAssignment -Name 'sql-db-skus-assignment' -Scope '/subscriptions/{subscription-id}' -PolicyDefinition $definition -PolicyParameter $params
```

**Method 3: Using a JSON parameter file**

Create a file `params.json`:
```json
{
    "effect": {
        "value": "Deny"
    },
    "listOfSKUName": {
        "value": ["S0", "S1", "S2", "S3", "GP_Gen5_2", "GP_Gen5_4", "GP_Gen5_8"]
    },
    "listOfSKUId": {
        "value": []
    }
}
```

Then apply it:
```bash
az policy assignment create --name 'sql-db-skus-assignment' --scope '/subscriptions/{sub-id}' --policy 'sql-db-skus' --params '@params.json'
```

**Method 4: Using Azure Portal**
1. Navigate to **Azure Policy** → **Assignments** → **Assign policy**
2. Select the policy definition "Allowed SQL DB SKUs"
3. Under **Parameters**:
   - **Effect**: Choose "Deny" (block), "Audit" (report only), or "Disabled"
   - Enter the allowed SKU names as comma-separated values
4. Click **Review + create**

### Best Practices

- **Start with Audit**: Use `"effect": "Audit"` initially to assess current SKU usage without blocking deployments, then switch to `"Deny"` for enforcement
- **Use SKU Names**: For most scenarios, using SKU names (e.g., "S0", "GP_Gen5_4") is simpler and more maintainable than GUIDs
- **Combine Parameters**: The policy allows databases matching **either** SKU names or IDs (OR logic), providing flexibility
- **Consider Workloads**: 
  - **Basic/Standard (DTU)**: Dev/test and typical production workloads
  - **Premium (DTU)**: High-performance OLTP requiring low latency
  - **General Purpose (vCore)**: Balanced modern workloads with predictable compute needs
  - **Business Critical (vCore)**: Mission-critical apps needing highest resilience and performance
  - **Hyperscale (vCore)**: Applications requiring massive storage (up to 100 TB) and rapid scaling
  - **Serverless (vCore)**: Intermittent workloads benefiting from auto-pause and per-second billing
- **Cost Management**: Restrict to lower tiers (Basic, S0-S2, GP_Gen5_2-4) for non-production environments
- **Migration Path**: When migrating DTU to vCore, roughly: 100 DTUs ≈ 1 vCore (Standard/GP), 125 DTUs ≈ 1 vCore (Premium/BC)
- **Testing**: After applying/updating parameters, wait 15-30 minutes for policy propagation before testing enforcement

## Troubleshooting

### Policy Not Blocking Non-Allowed SKUs

If databases with non-allowed SKUs are being created despite the policy:

1. **Verify Policy Assignment**: Check the policy is assigned at the correct scope (subscription/resource group)
   ```cli
   az policy assignment show --name 'sql-db-skus-assignment' --scope '/subscriptions/{sub-id}'
   ```

2. **Check Enforcement Mode**: Ensure enforcement mode is "Default" (not "DoNotEnforce")
   ```cli
   az policy assignment show --name 'sql-db-skus-assignment' --query 'enforcementMode'
   ```

3. **Wait for Propagation**: Azure Policy changes take 15-30 minutes to propagate. Wait before testing.

4. **Verify Parameters**: Confirm the SKU name is spelled exactly as in [official documentation](https://learn.microsoft.com/azure/azure-sql/database/resource-limits-vcore-single-databases):
   ```cli
   az policy assignment show --name 'sql-db-skus-assignment' --query 'parameters'
   ```

5. **Check Compliance State**: View compliance for existing resources
   ```cli
   az policy state list --resource-group <rg-name> --policy-assignment 'sql-db-skus-assignment'
   ```

6. **Field Availability**: The policy checks three fields (`requestedServiceObjectiveId`, `requestedServiceObjectiveName`, `sku.name`). Some SKUs may report differently during CREATE vs UPDATE operations. If issues persist, use Activity Log to inspect the actual field values during database creation:
   ```cli
   az monitor activity-log list --resource-group <rg-name> --start-time 2024-01-01 --query "[?contains(resourceType, 'Microsoft.SQL/servers/databases')]"
   ```

### Common Issues

- **Case Sensitivity**: SKU names are case-sensitive. Use `GP_Gen5_2` not `gp_gen5_2`
- **Serverless SKUs**: Serverless SKUs use `_S_` in the name (e.g., `GP_S_Gen5_1`, `HS_S_Gen5_2`)
- **Missing SKU**: If a new Azure SKU is released and not listed, check [vCore limits](https://learn.microsoft.com/azure/azure-sql/database/resource-limits-vcore-single-databases) and [DTU limits](https://learn.microsoft.com/azure/azure-sql/database/resource-limits-dtu-single-databases) for the exact name
- **Policy Scope**: Policy only applies to new database creations and SKU changes (scale operations), not existing databases
