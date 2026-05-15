# LinkedIn — Setup Session Manager on EC2 (basics)

> Companion to [Setup-Session-Manager-EC2-Basics.md](Setup-Session-Manager-EC2-Basics.md)  
> Status: draft · May 2026

---

**Post headline:**  
Session Manager in five beats — role, agent, outbound HTTPS, fleet, connect.

---

Session Manager isn’t “SSH with extra steps.” It’s **IAM + an agent that calls AWS outbound**.

Minimal checklist:

1. **Instance profile** with **`AmazonSSMManagedInstanceCore`** so the agent can register and take sessions.  
2. **SSM Agent** running (already there on many Linux AMIs).  
3. **Outbound 443** to AWS APIs — public subnet + IGW, private + NAT, or VPC endpoints in locked-down nets.  
4. **Fleet Manager** shows the instance as managed — if not, it’s almost always role or network.  
5. **Humans get IAM** to `StartSession`; instances get the **instance role**, not your user keys.

Full step-by-step draft (console-minded, still beginner): [Link to Medium article]

**Want the “why no inbound ports?” story first?** That’s a separate short piece — happy to link both in comments.

#AWS #SystemsManager #EC2 #CloudSecurity #DevOps
