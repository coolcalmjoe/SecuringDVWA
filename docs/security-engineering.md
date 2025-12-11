---

#### **2. Security Engineering Page (`docs/security-engineering.md`)**
# Security Engineering: Architecture & Hardening

## 1. The Threat Model
The default DVWA installation is insecure by design, often running as root with a flat network structure. This project re-architects the application to simulate a secure enterprise deployment.

**Identified Risks:**
* **Flat Network:** If the web app is compromised, the attacker has direct access to the Database.
* **Privileged Execution:** Running containers as root allows container breakouts.
* **Unfiltered Traffic:** Direct access to the application bypasses perimeter defenses.

## 2. Zero-Trust Network Segmentation
I implemented a strict network isolation strategy using Docker Compose networks to create a DMZ and Trusted Zone.

```mermaid
flowchart LR
    subgraph "Public Internet"
        User((User/Attacker))
    end

    subgraph "Host Machine"
        subgraph "Proxy-Net (DMZ)"
            WAF[Nginx WAF]
        end

        subgraph "Bridge Zone"
            App[DVWA App]
        end

        subgraph "DB-Net (Trusted)"
            DB[(MariaDB)]
        end
    end

    User --Port 80--> WAF
    WAF --Proxy Pass--> App
    App --3306--> DB
    User -.->|BLOCKED| App
    User -.->|BLOCKED| DB
    
    style DB fill:#bfb,stroke:#333
    style WAF fill:#fbb,stroke:#333
