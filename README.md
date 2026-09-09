# AZ-104 Identity & Governance Lab

A hands-on Azure project covering the "Manage identities and governance" domain of the AZ-104 exam.

## What this project covers

- **Microsoft Entra ID (Azure AD)** — creating users, creating a security group, managing group membership
- **RBAC (Role-Based Access Control)** — assigning built-in roles, building and assigning a custom role, understanding scope
- **ARM Templates** — JSON-based Infrastructure as Code, deploying a Storage Account
- **Bicep** — the same deployment rewritten in Microsoft's simplified IaC language
- **Governance controls** — resource tags, resource locks (CanNotDelete), and Azure Policy (assignment, compliance checking, and remediation)
- **Management Groups** — organizing subscriptions above the subscription level

## Files in this repo

- `arm-template.json` — ARM template that deploys a Storage Account
- `main.bicep` — the same deployment, written in Bicep
- `custom-role.json` — a custom RBAC role definition ("AZ104 VM Operator") that allows viewing resources and starting/restarting VMs only

## What We Did

**1. Entra ID** -- created 2 test users and a security group, then added both users to it.

    az ad user create --display-name "Test User One" --user-principal-name testuser1@<tenant> --password "..."
    az ad group create --display-name "AZ104-Test-Group" --mail-nickname "az104testgroup"
    az ad group member add --group "AZ104-Test-Group" --member-id $USERID

**2. RBAC** -- assigned the built-in Reader role to the group, then built and assigned a custom role.

    az role assignment create --assignee $GROUPID --role "Reader" --scope /subscriptions/$SUBID/resourceGroups/rg-az104-governance-project
    az role definition create --role-definition custom-role.json
    az role assignment create --assignee $USERID --role "AZ104 VM Operator" --scope /subscriptions/$SUBID/resourceGroups/rg-az104-governance-project

**3. ARM Template** -- deployed a Storage Account from `arm-template.json`.

    az deployment group create --resource-group rg-az104-governance-project --template-file arm-template.json --parameters storageAccountName=az104gov$RANDOM

**4. Bicep** -- deployed the exact same resource from `main.bicep`.

    az deployment group create --resource-group rg-az104-governance-project --template-file main.bicep --parameters storageAccountName=az104bicep$RANDOM

**5. Tags** -- tagged the resource group for organization and cost tracking.

    az group update --name rg-az104-governance-project --set tags.Project=AZ104 tags.Owner=Sadia tags.Environment=Lab

**6. Resource Lock** -- locked the resource group against deletion, then confirmed it actually blocks deletion.

    az lock create --name DontDeleteLab --resource-group rg-az104-governance-project --lock-type CanNotDelete

**7. Azure Policy** -- required a `CostCenter` tag, watched it flag as NonCompliant, added the tag, confirmed it turned Compliant.

    az policy assignment create --name require-tag-rg --policy 96670d01-0a4d-4649-9c89-2d3abc0a5025 --scope /subscriptions/$SUBID/resourceGroups/rg-az104-governance-project --params '{"tagName": {"value": "CostCenter"}}'
    az policy state trigger-scan --resource-group rg-az104-governance-project

**8. Management Group** -- created one and moved the subscription under it.

    az account management-group create --name az104-mg-lab --display-name "AZ104 Lab MG"
    az account management-group subscription add --name az104-mg-lab --subscription $SUBID
