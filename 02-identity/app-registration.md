# App Registrations and Enterprise Applications

[← Back to README](../README.md) · Lab 12

## Three concepts that are often confused

| Concept | What it is | Where |
|---|---|---|
| **App registration** (application object) | The **global definition** of the app. Exists **once**, in the app's home tenant | Entra ID → App registrations |
| **Service principal** | The app's **local instance in a tenant**. This is what gets **permissions and roles** | One per tenant that uses the app |
| **Enterprise application** | The **portal view of the service principal** | Entra ID → Enterprise applications |

```
App registration (home tenant)  ── definition, one per app
        │
        ├── Service principal in tenant A  ── gets roles here
        └── Service principal in tenant B  ── gets roles here
                (shown as Enterprise applications)
```

## IDs
- **Same Client ID** (Application ID) for the app registration and its service principals
- **Different Object IDs** – they are different objects

## API permissions

| Type | The app acts… | Example |
|---|---|---|
| **Delegated** | **On behalf of a signed-in user** – can never do more than the user can | A web app reads the user's own email |
| **Application** | **On its own**, with no user signed in | A background job reads all groups |

- Some permissions need **admin consent**, for example **`Group.Read.All`**
- Application permissions always need admin consent

## Exam tips
- App registration = **definition**, service principal = **instance that gets access**, enterprise application = **portal view of the service principal**
- Same Client ID, different Object IDs
- Delegated = **with a user**, Application = **without a user**

## Related
- [Service Principals](service-principals.md)
- [Managed Identities](managed-identities.md) – a service principal managed by Azure
- [Federation](federation.md)
