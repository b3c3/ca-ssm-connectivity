# LinkedIn — No Inbound Ports Needed (concept)

> Companion to [No-Inbound-Ports-How-SSM-Connectivity-Works.md](No-Inbound-Ports-How-SSM-Connectivity-Works.md)  
> Status: draft · May 2026

---

**Post headline (first line):**  
No inbound ports needed — here's how AWS Session Manager actually connects.

---

"If it's outbound-only, why do I have to click **Connect**?"

That's the number one question I hear about AWS Systems Manager Session Manager. It sounds like a contradiction. It isn't.

**The old way (SSH):**  
You knock on the server's front door — Port 22. The door has to be open. If your security group blocks inbound traffic, you're locked out.

**The Session Manager way:**  
The SSM Agent on your instance is like a receptionist who keeps calling "central dispatch" (AWS) to ask if anyone's waiting. When you click Connect, you're not knocking on the instance. You're telling AWS: "I'd like a session with this server." The next time the agent checks in, AWS says: "Someone's waiting — open a secure line back to me."

**What you get:**

- No inbound ports (no Port 22 or 3389 wide open)
- No SSH keys on your laptop to lose or rotate
- Access controlled through IAM — who can connect, and to which instances

The instance dials out. You never need a listener on the public internet.

This piece is **about how that connectivity works** — not a console walkthrough. For turning Session Manager on in AWS (roles, launch, attach), see the companion setup notes when published.

Full story on Medium: [Link to Medium article]

#AWS #CloudSecurity #DevOps #SystemsManager #EC2
