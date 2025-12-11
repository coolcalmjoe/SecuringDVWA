# Automated "Grey Box" DevSecOps Laboratory

![Pipeline Status](https://img.shields.io/badge/Pipeline-Passing-success) ![Security Grade](https://img.shields.io/badge/Security_Grade-A-success) ![Stack](https://img.shields.io/badge/Tech-Docker%20|%20GitLab%20|%20Python%20|%20DefectDojo%20|%20DependencyTrack%20|%20Trivy%20|%20Semgrep%20|%20Sonarqube%20|%20xDebug%20|%20openRASP%20|%20OPNsense%20|-blue)

## 1. Executive Summary

This is an ongoing research project focused on architecting a **Secure SDLC** for the "Damn Vulnerable Web App" (DVWA). Rather than just exploiting vulnerabilities, I am building the ecosystem required to manage them. I established a CI/CD pipeline in GitLab to enforce automated security gates, utilizing **Semgrep**, **Trivy**, and **SonarQube** for static analysis, and **OWASP ZAP** for dynamic validation.

Simultaneously, I am constructing a layered defense architecture to move from a flat network to a segmented, monitored environment. This includes **database isolation**, an **Nginx Proxy Manager** for future WAF implementation, and network monitoring with **Suricata**. To facilitate deep-dive research, I have instrumented the application runtime with **OpenRASP** (in monitoring mode) and **Xdebug**. This setup enables **"Grey Box" analysis**, allowing me to correlate external HTTP attacks directly with internal PHP code execution before actively tuning the defenses.

**Objective:** To simulate the hardening of a legacy application by orchestrating four core domains: Security Engineering, DevSecOps, Vulnerability Management, and AppSec Research. I am utilizing this lab to:
* Map technical exploits to **NIST SP 800-53** control failures.
* Harden the container runtime according to **CIS Docker Benchmarks**.
* Measure the application's posture against the **OWASP ASVS (Level 2)** verification standard.

---

## 2. Lab Architecture & Workflow

The architecture enforces a strict separation between the **Automated Build Plane** (CI/CD) and the **Runtime Research Plane** (The Lab). The runtime environment simulates a defensible enterprise network, moving away from flat topology into a segmented "DMZ" model where the application, database, and attacker zones are strictly isolated.

```mermaid
graph LR
    %% Zone Styles
    classDef internet fill:#ffeded,stroke:#ff3333,stroke-width:2px,rx:20,ry:20;
    classDef dmz fill:#ffffe0,stroke:#e6b800,stroke-width:2px,rx:20,ry:20;
    classDef trusted fill:#e6ffe6,stroke:#009900,stroke-width:2px,rx:20,ry:20;
    classDef component fill:#ffffff,stroke:#333,stroke-width:1px;

    %% ZONE: INTERNET
    subgraph Internet_Zone ["Internet Zone"]
        direction TB
        Kali["Kali Laptop<br/>(Attacker)"]:::component
    end

    %% ZONE: DMZ
    subgraph DMZ_Zone ["DMZ (Public Facing)"]
        direction TB
        NPM["Nginx Proxy<br/>Manager"]:::component
        
        subgraph DVWA_Host ["DVWA Docker Host"]
            direction TB
            DVWA["DVWA Container<br/>(PHP + OpenRASP)"]:::component
            HostShell["Host Shell<br/>(lnav Logs)"]:::component
        end
    end

    %% ZONE: TRUSTED VLAN
    subgraph Trusted_Zone ["Trusted VLAN (Internal)"]
        direction TB
        MariaDB[("MariaDB<br/>(Database)")]:::component
        VSCode["VS Code<br/>(Monitor/Debug)"]:::component
    end

    %% --- NETWORK FLOWS ---
    Kali -- "HTTP Attack" --> NPM
    NPM -- "Proxy Pass" --> DVWA

    DVWA -- "SQL Traffic" --> MariaDB
    DVWA -. "Volume Mount" .- HostShell
    
    %% Xdebug Flow
    DVWA -- "Xdebug :9003" --> HostGateway["Host Gateway"]:::component
    HostGateway -- "SSH Reverse Tunnel" --> VSCode

    %% Apply Styles
    class Internet_Zone internet
    class DMZ_Zone dmz
    class Trusted_Zone trusted
```

---

## 3. Project Domains
Click the links below for deep-dives into the implementation details of each domain.

| Domain | Focus Area | Key Technologies |
| :--- | :--- | :--- |
| [**Security Engineering**](docs/security-engineering.md) | Network Segmentation & Defense | Docker Networks, Nginx Proxy Manager, OPNsense, Suricata |
| [**DevSecOps**](docs/devsecops.md) | CI/CD Automation & Supply Chain | GitLab CI, Trivy, Semgrep, SonarQube, Dependency-Track |
| [**AppSec Research**](docs/appsec-research.md) | Exploit Analysis & "Grey Box" Debugging | Xdebug, OpenRASP, OWASP ZAP, Burp Suite |
| [**Vulnerability Management**](docs/vuln-mgmt.md) | Centralized Aggregation & Metrics | DefectDojo, OpenVAS |

---

## 4. Key Differentiators

* **The "Grey Box" Feedback Loop:** This lab bridges the gap between Red Team and Blue Team. I utilize a **Reverse SSH Tunnel** to connect the remote container's Xdebug service to my local IDE, allowing me to step through code execution while I validate DAST results.
* **Runtime Instrumentation:** The application runtime is **heavily instrumented** and **"sensor-rich."** While the application remains vulnerable by design for research, I integrated **OpenRASP** (in monitoring mode) and **Xdebug** to capture granular telemetry of every exploit, providing "Glass Box" visibility that standard labs lack.
* **Intelligent Reporting Orchestration:** Vulnerability data is not just dumped into a console; it is routed to the correct system of record:
    * **DefectDojo:** Aggregates actionable security findings from ZAP, Semgrep, openVAS, and Trivy.
    * **Dependency-Track:** Monitors long-term Supply Chain risk (SBOMs).
    * **SonarQube:** Tracks Code Quality and Technical Debt.

---

*Created by Joseph Dennis*
