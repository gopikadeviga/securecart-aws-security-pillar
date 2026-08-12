# SecureCart — Secure Multi-Tier Web Application on AWS

A hands-on implementation of the **AWS Well-Architected Framework (Security Pillar)**. SecureCart is a multi-tier web application where the application and database tiers are fully isolated from the public internet, protected by layered security groups, a web application firewall, and keyless administrative access.

> **Status:** Completed and verified end-to-end. Built via the AWS Console in `us-east-1`(N. Virginia).

---

## Overview

SecureCart demonstrates **defense in depth**: multiple independent layers of protection so that no single failure exposes the system. A public facing load balancer is the only internet reachable component. The application runs on a private EC2 instance with no public IP, and the database is unreachable from the internet : a design proven with live connectivity tests (see [Verification](#verification)).

**Key principle demonstrated:** the application *can* reach the database; the public internet *cannot*.

---

## Architecture

<img width="905" height="654" alt="securecart-architecture-diagram" src="https://github.com/user-attachments/assets/96d68176-591f-4c11-9a66-5d9f5db14244" />

---

### Network topology
| Subnet | AZ | Contents |
|---|---|---|
| Public 1 | us-east-1a | ALB, NAT Gateway |
| Public 2 | us-east-1b | ALB |
| Private 1 | us-east-1a | EC2 (application), RDS |
| Private 2 | us-east-1b | RDS standby slot |

---

## Tech stack

**AWS services:** VPC, Subnets, Internet Gateway, NAT Gateway, Route Tables, Security Groups, EC2, RDS (MySQL), Application Load Balancer, AWS WAF, IAM, Systems Manager (Session Manager)

**Region:** us-east-1

---
## Verification

The isolation model was proven with two connectivity tests to the RDS database on port 3306:

**From inside the private EC2 instance (via SSM Session Manager):**
```
RDS REACHABLE
```
The application tier can reach the database, the intended path works.

**From the public internet (local machine):**
```
TcpTestSucceeded : False
RemoteAddress    : 10.0.135.27   (private VPC IP, no public route)
```
The database is unreachable from outside the VPC , isolation confirmed.

*The database endpoint resolves to a private `10.0.x.x` address with no route from the public internet, so external connections time out. This is defense in depth working as designed.*

### SSM-session (Keyless access)
<img width="1191" height="319" alt="Keyless access" src="https://github.com/user-attachments/assets/0d262309-f548-4ef0-b949-dd7f47e62ef7" />

### RDS blocked from internet
<img width="1085" height="305" alt="TcpTest" src="https://github.com/user-attachments/assets/60e7aa6f-9a4d-4da0-a7a2-53027fd139c6" />

---

### ALB-backend response 
<img width="831" height="305" alt="ALB-backend" src="https://github.com/user-attachments/assets/ada4e617-1b11-4332-ad04-6dadc34bc960" />



### WAF associated
<img width="775" height="699" alt="WAF-protection-pack" src="https://github.com/user-attachments/assets/84b0beea-7716-49d9-b075-d79ca37c505c" />


