# LinkedIn — Session Manager network scenarios (advanced)

> Companion to [Session-Manager-Network-Scenarios-Advanced.md](Session-Manager-Network-Scenarios-Advanced.md)  
> Status: draft · May 2026

---

**Post headline:**  
Three network shapes for Session Manager — public, NAT, or endpoints-only.

---

“No inbound ports” doesn’t mean “no networking homework.” The **instance still has to reach AWS outbound on 443** — just not your SSH port.

Three patterns I draw on repeat:

**A — Public subnet + Internet Gateway**  
Quick labs. Instance routes `0.0.0.0/0` to an IGW; outbound HTTPS hits AWS directly.

**B — Private subnet + NAT Gateway**  
Production-friendly default for many teams. No public IP on the instance; outbound still hits AWS via NAT → IGW.

**C — Private subnet + VPC interface endpoints**  
Maximum isolation. No internet egress; `ssm`, `ssmmessages`, `ec2messages` (and friends) accessed via endpoints — endpoint SGs must allow 443 from your workloads.

Same Session Manager story in all three: **agent + instance role + outbound path**. Only the **path** changes.

Deep dive (routing, SG mindset, comparison table): [Link to Medium article]

#AWS #VPC #SystemsManager #CloudArchitecture #Security
