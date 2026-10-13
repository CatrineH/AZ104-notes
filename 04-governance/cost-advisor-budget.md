# Cost Management, Advisor, and Budgets

[← Back to README](../README.md) · Governance

## Microsoft Cost Management
Portal: **Cost Management + Billing**

| Feature | What it does |
|---|---|
| **Cost analysis** | Explore costs by **scope, service, resource, location, or tag** |
| **Budgets** | Set a spending limit and get **alerts** |
| **Cost alerts** | Notifications when spending crosses a threshold or looks unusual |
| **Exports** | Send cost data to a **storage account** on a schedule |

> Cost data has a **delay of up to 24 hours** – a VM created this morning may not show up until tomorrow.

---

## Budgets

### Settings

| Setting | Options |
|---|---|
| **Scope** | Management group, subscription, resource group (or billing account) |
| **Type** | **Cost** budget (money) or **Usage** budget (quantity) |
| **Amount** | e.g. 5 000 kr |
| **Reset period** | Monthly, quarterly, or annually |
| **Filters** | e.g. only resources with tag `environment=dev` |

### Alert conditions – two types

| Type | Triggers when… |
|---|---|
| **Actual** | Real spending **has reached** the threshold, e.g. 80% |
| **Forecasted** | Azure **predicts** spending will reach the threshold by the end of the period |

### Budgets don't stop spending
 A budget **only alerts**. It never shuts down resources by itself.

To **do something** at a threshold, connect an **action group**.

### Budgets roll up
A subscription budget measures **everything below it** – all resource groups and resources. Budgets are **not inherited** (see [Inheritance](inheritance.md)).

---

## Action groups
Decide **what happens** when a threshold is reached:

| Action | Example |
|---|---|
| **Email / SMS / push / voice** | Notify the team lead |
| **Webhook** | Post to Teams or another system |
| **Automation runbook** | **Stop or deallocate VMs** |
| **Logic App** | A workflow, e.g. create a ticket |
| **Azure Function** | Custom code |

> Action groups on budgets work at **subscription and resource group** scope.

### Example: stop dev VMs at 100%

```
Budget sub-dev: 5 000 kr/month
├── 80% actual     → email to the team
├── 100% forecast  → email to the manager
└── 100% actual    → action group → runbook → deallocate all VMs in rg-dev
```

---

## Cost alerts

| Alert type | Triggers when… |
|---|---|
| **Budget alerts** | A budget threshold is reached |
| **Anomaly alerts** | Spending is **unusually high** compared to normal |
| **Credit alerts** | Azure credits (e.g. prepaid commitment) are running out |
| **Department spending quota alerts** | A department reaches its quota (Enterprise Agreement) |

---

## Azure Advisor
A **free** service that gives personalized **recommendations**, based on the **Well-Architected Framework (WAF)**.

### Five categories

| Category | Example recommendation |
|---|---|
| **Cost** | Right-size or shut down underused VMs, buy reservations |
| **Security** | Enable MFA, fix Defender for Cloud findings |
| **Reliability** | Use availability zones, enable backup |
| **Operational excellence** | Fix deprecated settings, follow best practices |
| **Performance** | Upgrade storage, scale up overloaded resources |

- **Advisor score** shows how well you follow the recommendations
- The **CPU threshold** for "underused VM" can be adjusted (5–100%, default 5%)
- Recommendations can be **postponed or dismissed**, and you can set alerts on new ones

---

## Ways to save money on compute (VMs)

| Option | How it saves | Best for |
|---|---|---|
| **Deallocate / auto-shutdown** | **Stop paying for compute** when the VM isn't needed | Dev/test VMs at night and weekends |
| **Right-sizing** | Pick a smaller or better-suited VM size | VMs with low CPU or memory use |
| **Azure Hybrid Benefit** | Use existing **Windows Server or SQL Server licenses** with Software Assurance | Companies moving from on-premises |
| **Spot VMs** | Up to ~90% discount on **unused capacity** – can be **evicted** | Batch jobs, interruptible work |
| **Reservations** | **1 or 3 year** commitment to a VM **size and region** | Stable workloads running 24/7 |
| **Savings plans** | **1 or 3 year** commitment to an **hourly spend**, across VM families and regions | Changing workloads |
| **Dev/Test pricing** | Lower prices for **non-production** (Visual Studio subscribers) | Dev and test subscriptions |

> **Stopped inside the OS ≠ deallocated.** Shutting down from Windows or Linux still bills compute. Stop from the **portal** (status **Stopped (deallocated)**) to stop paying. Disks are still billed either way.

### Which saving? Read the wording

| The question says… | Answer |
|---|---|
| "**one VM size**, **one region**, runs 24/7, 3 years" | **Reservation** |
| "different VM **families** or **regions**", "flexible" | **Savings plan** |
| "existing **licenses** with **Software Assurance**" | **Azure Hybrid Benefit** |
| "can be **interrupted**", "batch processing" | **Spot VMs** |
| "only used during **office hours**" | **Auto-shutdown / deallocate** |
| "**low CPU** use", "underutilized" | **Right-size** |
| "dev/test environment", "Visual Studio subscription" | **Dev/Test pricing** |

### Right-sizing – VM families

| Family | Optimized for | Series |
|---|---|---|
| **General purpose** | Balanced CPU and memory | B, D |
| **Compute optimized** | High CPU | F |
| **Memory optimized** | High memory, databases | E, M |
| **Storage optimized** | High disk throughput | L |
| **GPU** | Graphics, AI | N |

Pick the **family** that matches the workload, then the **smallest size** that does the job.

---

## Other cost tools

| Tool | Use it to |
|---|---|
| **Pricing calculator** | Estimate the cost of **new** Azure resources |
| **TCO calculator** | Compare **on-premises** costs with Azure |
| **Tags** | Split costs by **department, project, or environment** in cost analysis |

## Roles

| Role | Can |
|---|---|
| **Cost Management Reader** | View costs and budgets |
| **Cost Management Contributor** | Create and manage budgets and exports |

## Exam tips
- Budgets **alert** – they **don't stop** resources. Use an **action group** to act
- Two alert types: **actual** and **forecasted**
- Cost data can be **up to 24 hours** late
- Advisor has **five** categories, based on the **Well-Architected Framework**
- **Deallocate** to stop paying for compute – not shut down from inside the OS
- Reservation = **specific size and region**. Savings plan = **flexible across families and regions**

## Related
- [Tags](tags.md)
- [Inheritance](inheritance.md)
- [Scope Hierarchy](scope-hierarchy.md)
