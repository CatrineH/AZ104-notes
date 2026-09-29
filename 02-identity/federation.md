# Federation

[← Back to README](../README.md)

## What is it?
**Federation** creates a trust relationship between identity providers. Instead of storing credentials, access is granted when specific authentication requirements or conditions are met.

## Key points
- No long-term secrets are exchanged between systems
- One system trusts tokens issued by another
- Example: a GitHub Actions pipeline authenticates to Azure using **workload identity federation**, with no client secret stored in GitHub

## Related
- [Service Principals](service-principals.md)
