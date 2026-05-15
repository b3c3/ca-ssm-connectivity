# Session Manager Network Scenarios — VPC Paths and Endpoints (Advanced)

> Advanced companion · Medium / long form  
> Status: draft · May 2026  
> **LinkedIn draft:** [Session-Manager-Network-Scenarios-Advanced-LinkedIn.md](Session-Manager-Network-Scenarios-Advanced-LinkedIn.md)

---

## Audience

You understand **[how Session Manager connectivity works](No-Inbound-Ports-How-SSM-Connectivity-Works.md)** and have seen **[basic EC2 setup](Setup-Session-Manager-EC2-Basics.md)**. This article compares **how network paths differ** when the instance lives in a **public subnet**, a **private subnet with NAT**, or a **private subnet with VPC interface endpoints only**.

It stays diagram-oriented at a **routing + security group** level — not a full Terraform workshop.

---

**Title (working):** Session Manager Network Paths — Public, NAT, and VPC Endpoints

**Subtitle (working):** Three ways your instance reaches AWS so Session Manager can work — and what to draw on the whiteboard.

---

### What must always be true (all scenarios)

- **SSM Agent** on the instance, **instance profile** with **`AmazonSSMManagedInstanceCore`** (baseline).  
- **Outbound HTTPS** from the instance to AWS APIs Session Manager depends on — either over the **public internet** (direct or via NAT) or via **interface VPC endpoints** inside the VPC.  
- **Security groups:** Session Manager access **does not require inbound rules** from the internet for Port 22/3389; focus on allowing **egress** your architecture needs (often `0.0.0.0/0` on 443 for simplicity, or tighter corporate egress).  
- **DNS resolution** must reach AWS service names — for endpoint-only designs, **enable Private DNS** on interface endpoints when you use that pattern so `*.amazonaws.com` resolves to the endpoint inside the VPC.

*(Exact endpoint service IDs vary slightly by region — confirm in the VPC console or docs for your region.)*

---

### Scenario A — Instance in a public subnet (outbound via Internet Gateway)

**Rough sketch**

- Subnet route table: `0.0.0.0/0` → **Internet Gateway (IGW)**.  
- Instance often has a **public IP** (or EIP) so return traffic from the internet works symmetrically for that path.  
- Instance initiates **outbound TCP 443** to AWS endpoints over the IGW.

**Security group mindset**

- Inbound: whatever your app needs (often none from `0.0.0.0/0` for SSH).  
- Outbound: allow **443** toward AWS (many teams use broad egress to start, then tighten).

**When teams use it**

Labs, small accounts, or workloads that already sit in public subnets with a known blast radius.

---

### Scenario B — Instance in a private subnet (outbound via NAT Gateway)

**Rough sketch**

- Instance **no public IP**.  
- Private subnet route table: `0.0.0.0/0` → **NAT Gateway** in a **public** subnet; NAT subnet has route to **IGW**.  
- Instance still reaches AWS APIs over **internet**, but **inbound** from the internet cannot reach the instance directly — classic improvement over Scenario A for many apps.

**Security group mindset**

- Same Session Manager story: **no inbound SSH** required for SSM shell access.  
- Outbound **443** must succeed via NAT (ensure NAT is healthy and NACLs allow ephemeral return traffic as usual).

**When teams use it**

Production-style workloads that should not be reachable from the internet but still need outbound connectivity without maintaining endpoint lists.

---

### Scenario C — Instance in a private subnet (VPC interface endpoints only)

**Rough sketch**

- No internet egress — **no default route** to IGW/NAT (or tightly scoped egress).  
- Create **Interface VPC endpoints** for the Session Manager “family” of services (typically including **`ssm`**, **`ssmmessages`**, **`ec2messages`** — some environments also endpoint **`kms`** if encryption policies demand it).  
- Traffic to those APIs stays **inside AWS’s network** from your VPC’s perspective.

**Security group mindset**

- Endpoint ENIs get their **own security groups**: allow **inbound 443** from instances (or subnets) that must use the endpoints.  
- Instances still **do not** open inbound for Session Manager clients from the internet.

**When teams use it**

Strict private workloads, regulated networks, or “no internet egress” mandates.

---

### Quick comparison table

| Scenario | Path to AWS APIs | Typical inbound from internet | Complexity |
|----------|------------------|------------------------------|------------|
| A — Public subnet + IGW | Direct via IGW | Often avoid SSH from `0.0.0.0/0`; still public footprint | Lowest |
| B — Private + NAT | Via NAT → IGW | Instance not directly reachable | Medium |
| C — Private + endpoints | Via interface endpoints | Instance not directly reachable | Higher (more endpoints/SGs) |

---

### Troubleshooting lens (shared)

If the instance never appears under **Fleet Manager**:

- Instance role missing **`AmazonSSMManagedInstanceCore`** or wrong profile attachment.  
- Agent not running or AMI without agent.  
- **No route/DNS path** to required endpoints — wrong routes, missing endpoints in scenario C, or endpoint SG blocking 443 from the instance subnets.

---

### Related pieces

- **[How connectivity works](No-Inbound-Ports-How-SSM-Connectivity-Works.md)**  
- **[Basic setup checklist](Setup-Session-Manager-EC2-Basics.md)**  

**Official references**

- [VPC endpoints for Systems Manager](https://docs.aws.amazon.com/systems-manager/latest/userguide/setup-create-vpc.html)  
- [Session Manager troubleshooting](https://docs.aws.amazon.com/systems-manager/latest/userguide/session-manager-troubleshooting.html)  

---

<!-- Publishing notes
Consider one Mermaid diagram per scenario (route table + arrows) in a later revision.
-->
