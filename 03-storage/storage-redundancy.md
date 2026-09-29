# Azure Storage Redundancy

[← Back to README](../README.md)

Azure always keeps multiple copies of your data. The redundancy option decides **where** the copies live.

## Options

| Option | Copies and location | Protects against |
|---|---|---|
| **LRS** | 3 copies in one datacenter | Disk or rack failure |
| **ZRS** | 3 copies across 3 availability zones | Datacenter or zone failure |
| **GRS** | LRS in primary + LRS in paired region | Regional outage |
| **RA-GRS** | GRS + read access to secondary | Regional outage, reads still work |
| **GZRS** | ZRS in primary + LRS in paired region | Zone failure and regional outage |
| **RA-GZRS** | GZRS + read access to secondary | Maximum availability |

## Reminders
- **RA-** = read access to the secondary region, for example `mystorage-secondary.blob.core.windows.net`
- Replication to the secondary region is **asynchronous** – recent writes can be lost in a failover
- **Premium** storage accounts support only LRS and ZRS
- Cost order, cheapest first: LRS → ZRS → GRS → RA-GRS → GZRS → RA-GZRS

## Scenario examples
| Requirement | Answer |
|---|---|
| Cheapest, data can be recreated | LRS |
| Survive a datacenter failure, data stays in one region | ZRS |
| Read data during a regional outage | RA-GRS or RA-GZRS |
