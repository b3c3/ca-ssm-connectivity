# Setup Session Manager on EC2 — Basics

> Hands-on setup · Medium / long form  
> Status: draft · May 2026  
> **LinkedIn draft:** [Setup-Session-Manager-EC2-Basics-LinkedIn.md](Setup-Session-Manager-EC2-Basics-LinkedIn.md)

---

## Who this is for

You already know **why** Session Manager is attractive (no inbound SSH, IAM-based access). This draft walks through **what to turn on in AWS** so a typical EC2 instance can register with Systems Manager and accept a session — at a simple, checklist level.

**Prerequisite concepts:** read **[No Inbound Ports Needed — How SSM Connectivity Works](No-Inbound-Ports-How-SSM-Connectivity-Works.md)** if the “outbound-only” model is still fuzzy.

**Going deeper on VPC routing and endpoints:** **[Session Manager Network Scenarios (Advanced)](Session-Manager-Network-Scenarios-Advanced.md)**.

---

**Title (working):** Setup Session Manager on EC2 — Roles, Agents, and Your First Session

**Subtitle (working):** A minimal checklist to get an instance talking to Systems Manager and ready for Session Manager.

---

### What you need (high level)

1. **SSM Agent** on the instance — present by default on many Amazon Linux and Ubuntu AMIs; install or update if yours is bare or old.
2. **Outbound HTTPS (443)** from the instance to AWS Session Manager and related endpoints — via internet route (public subnet or NAT) **or** VPC interface endpoints in locked-down VPCs.
3. **An IAM instance profile** attached to the EC2 instance with permissions that let the agent register and receive sessions (see below).
4. **IAM permissions for people** who start sessions — separate from the instance role; usually `ssm:StartSession` (and related) scoped to the instances or tags you trust.

Network details vary by subnet design; the advanced article breaks down common patterns.

---

### Step 1 — Create an IAM role for the instance

1. In IAM, create a **role** that **AWS service → EC2** can assume.
2. Attach the AWS managed policy **`AmazonSSMManagedInstanceCore`**.  
   This is the usual baseline so the agent can register as a managed instance and participate in Session Manager.  
   *(Optional later: add policies for session logging to S3 or CloudWatch if your org requires transcripts.)*

Create an **instance profile** from this role (the console often does this when you create the role).

---

### Step 2 — Launch (or select) EC2 and attach the profile

1. Launch an instance in a subnet that can reach AWS APIs over **HTTPS outbound** (simplest case: public subnet with route to an Internet Gateway, or private subnet with NAT — see advanced scenarios).
2. On **Advanced details**, attach the **IAM instance profile** you created.
3. Security group: **no inbound rule is required for Session Manager shell access** itself; allow only what your workload needs (and optionally restrict outbound if your standards require it — just ensure SSM traffic can still reach AWS).

Wait a minute or two after boot for the agent to register.

---

### Step 3 — Confirm the instance appears in Fleet Manager

In the AWS console: **Systems Manager → Fleet Manager** (or **Node Management → Fleet Manager**, depending on console layout). Your instance should show as a **managed instance**.

If it never appears, the usual suspects are: missing/wrong instance profile, agent stopped or missing, or **no outbound path to SSM endpoints** (fixed by routing/NAT or VPC endpoints).

---

### Step 4 — Grant humans permission to start a session

Attach an IAM policy to users or roles who should connect. At minimum they need permission to call **`ssm:StartSession`** (and typically **`ssm:TerminateSession`**, **`ssm:ResumeSession`**) on the right resources. Many teams use AWS’s example policies as a starting point and tighten with tags or resource ARNs.

*(Exact JSON varies by org — align with your security team.)*

---

### Step 5 — Connect

Open **Systems Manager → Session Manager → Start session**, choose your instance, and connect. Alternatively use the AWS CLI **`aws ssm start-session`** with an authenticated profile that has the right IAM permissions.

---

### Optional — Session logging

If you need audit trails, enable **session logging** in Session Manager preferences (S3, CloudWatch Logs, or both). That adds IAM permissions on the **instance role** or **delivery role** depending on how AWS guides your chosen option — treat this as a follow-on step after basic connectivity works.

---

### Where to go next

- **[How SSM connectivity works](No-Inbound-Ports-How-SSM-Connectivity-Works.md)** — mental model  
- **[Network scenarios (advanced)](Session-Manager-Network-Scenarios-Advanced.md)** — public subnet, private + NAT, private + endpoints  

**Official guides**

- [Setting up Session Manager](https://docs.aws.amazon.com/systems-manager/latest/userguide/session-manager-getting-started.html)  
- [IAM instance permissions](https://docs.aws.amazon.com/systems-manager/latest/userguide/session-manager-getting-started.html#session-manager-permissions)  

---

<!-- Publishing notes
Medium tags suggestion: AWS, EC2, Systems Manager, DevOps, Security
-->
