# Service Principals

[← Back to README](../README.md)

## What is it?
A **Service Principal** is an identity used by an application or service to authenticate and access Azure resources, **without using a user account**.

## How it works
- Created from an **app registration** in Microsoft Entra ID
- Identified by an **Application (client) ID**
- Authenticates with a **client secret** or a **certificate**
- Gets access to resources through **Azure RBAC** role assignments

## Key points
- Used by apps, scripts, and automation (for example, CI/CD pipelines)
- Secrets expire and must be rotated
- Certificates are more secure than secrets

## Related
- [Managed Identities](managed-identities.md) – a service principal that Azure manages for you
- [Federation](federation.md)
