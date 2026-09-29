# Container Networking

[← Back to README](../README.md)

## Docker

### Bridge network
- A **private network that exists only on one server** (host)
- Containers on the same user-defined bridge network can reach each other **by container name**

```
Server (Company A)
└── Bridge network
    ├── web container
    └── payment container (port 9090)
```

### Port mapping
Containers are not reachable from outside by default. **Port mapping** forwards traffic from the host into a container:

```bash
docker run -p 9090:9090 company-a/payment
```

`-p <host port>:<container port>` – traffic arriving at the host on port 9090 is forwarded to port 9090 inside the `payment` container.

### Overlay network
Connects containers **across multiple servers**, so they can communicate as if they were on the same network.

## Kubernetes

| Concept | Key facts |
|---|---|
| **Pod** | Smallest unit. Gets **one unique IP address**. Pods are temporary – when a pod is recreated, it gets a **new IP address** |
| **Service** | Gives a group of pods a **permanent IP address and DNS name**, so other apps have a stable address to connect to |
| **Cluster** | A group of servers (nodes) running pods |

**Why Services matter:** pod IPs change all the time, so apps connect to the Service instead of directly to pods.

## In Azure
- **Azure Kubernetes Service (AKS)** – managed Kubernetes
- **Azure Container Instances (ACI)** – run single containers without managing servers

## Related
- [Network Fundamentals](network-fundamentals.md)
