# ca-ssm-connectivity

Beginner-friendly content explaining how **AWS Systems Manager Session Manager** connects to EC2 instances **without opening inbound ports** (no SSH/RDP listeners on your security groups), plus companion drafts for **hands-on setup** and **VPC network paths**.

## Articles

| Focus | Medium / long form | LinkedIn (short post) |
|------|-------------------|------------------------|
| **Concept** — how connectivity works (not a setup tutorial) | [No-Inbound-Ports-How-SSM-Connectivity-Works.md](articles/No-Inbound-Ports-How-SSM-Connectivity-Works.md) | [No-Inbound-Ports-How-SSM-Connectivity-Works-LinkedIn.md](articles/No-Inbound-Ports-How-SSM-Connectivity-Works-LinkedIn.md) |
| **Setup** — IAM role, instance profile, fleet check, first session | [Setup-Session-Manager-EC2-Basics.md](articles/Setup-Session-Manager-EC2-Basics.md) | [Setup-Session-Manager-EC2-Basics-LinkedIn.md](articles/Setup-Session-Manager-EC2-Basics-LinkedIn.md) |
| **Advanced** — network scenarios (public / NAT / VPC endpoints) | [Session-Manager-Network-Scenarios-Advanced.md](articles/Session-Manager-Network-Scenarios-Advanced.md) | [Session-Manager-Network-Scenarios-Advanced-LinkedIn.md](articles/Session-Manager-Network-Scenarios-Advanced-LinkedIn.md) |

Suggested reading order: **concept → setup → advanced network**.

### Repo layout

```
articles/           # Long-form drafts + matching LinkedIn companions (*.md)
Working-Research/   # Notes and source material for drafting
```

Research notes for early framing live in `Working-Research/Research-15May2026.md`.

## TODO
1. Be clear on this part not being an article on how to setup or use SSM Session Manager *(done in concept article header + separate setup article; LinkedIn companion updated)*
2. Diagrams - mostly mermaid on connectivity flow *(concept piece includes sequence diagram; add scenario diagrams in advanced article)*
3. Network set up Scenarios - e.g. show each scenario, the SG, the routes/endpoints and how connectivity is achieved (this might be a part two or advanced section) *(draft started: Session-Manager-Network-Scenarios-Advanced.md)*
    a. Instance in public subnet - outbound via IGW
    b. Instance in private subnet - outbound via NAT Gateway
    c. Instance in private subnet - outbound via VPC Endpoint
