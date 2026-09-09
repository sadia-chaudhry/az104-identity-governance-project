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

## How to deploy

or using Bicep:

## AZ-104 exam domain covered

Manage Azure identities and governance (~20-25% of the exam)
