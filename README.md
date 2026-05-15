# ca-ssm-connectivity

Beginner-friendly content explaining how **AWS Systems Manager Session Manager** connects to EC2 instances **without opening inbound ports** (no SSH/RDP listeners on your security groups).

## Article

**Title:** [No Inbound Ports Needed — How SSM Connectivity Works](articles/No-Inbound-Ports-How-SSM-Connectivity-Works.md)

| | |
|---|---|
| **File** | `articles/No-Inbound-Ports-How-SSM-Connectivity-Works.md` |
| **Status** | Primary draft (May 2026) |
| **Platforms** | Part A — LinkedIn · Part B — Medium |

### What it covers

- The “outbound-only but I click Connect” paradox
- SSH vs Session Manager — receptionist / central dispatch analogy
- Step-by-step flow when you connect (no API deep dive)
- Mermaid sequence diagram of the connection flow
- Why security groups can stay locked down (outbound HTTPS)
- Benefits, prerequisites, and outbound path caveats (NAT vs VPC endpoints)

### Repo layout

```
articles/          # Publishable drafts (LinkedIn + Medium in one file)
Working-Research/  # Notes and source material for drafting
```

Research notes for the first draft live in `Working-Research/Research-15May2026.md`.

## TODO
1. Diagrams - mostly mermaid on connectivity flow
2. Network set up Scenarios - e.g. show each scenario, the SG, the routes/endpoints and how connectivity is achieved (this might be a part two or advanced section)
    a. Instance in public subnet - outbound via IGW
    b. Instance in private subnet - outbound via NAT Gateway
    c. Instance in private subnet - outbound via VPC Endpoint