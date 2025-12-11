# Automated "Grey Box" DevSecOps Laboratory

![Pipeline Status](https://img.shields.io/badge/Pipeline-Passing-success) ![Security Grade](https://img.shields.io/badge/Security_Grade-A-success) ![Stack](https://img.shields.io/badge/Tech-Docker%20|%20GitLab%20|%20Python%20|%20DefectDojo-blue)

## 1. Executive Summary
This project transforms the "Damn Vulnerable Web App" (DVWA) into a hardened, enterprise-grade environment. Unlike standard pipelines that simply scan code, this project utilizes **"Grey Box" methodology**: baking security agents (OpenRASP) and debugging tools (Xdebug) directly into the build artifact to correlate external attacks with internal code execution.

**Objective:** To simulate a high-maturity Secure SDLC by orchestrating three core domains: Security Engineering, DevSecOps, and Advanced AppSec Research.

---

## 2. Lab Architecture & Workflow
The pipeline enforces a "Zero Trust" runtime environment and automates vulnerability reporting to centralized systems of record.

```mermaid
graph TD
    User[Attacker/Scanner] -->|HTTP Request| WAF(Nginx WAF)
    subgraph "DMZ / Public VLAN"
    WAF -->|Filtered Traffic| App[DVWA Container]
    end
    subgraph "Runtime Analysis"
    App -.->|Agent Logs| RASP[OpenRASP]
    App -.->|Step-Through| Debug[Xdebug]
    end
    subgraph "Trusted VLAN"
    App -->|SQL Queries| DB[(MariaDB)]
    end
    
    style WAF fill:#f9f,stroke:#333,stroke-width:2px
    style App fill:#bbf,stroke:#333,stroke-width:2px
    style DB fill:#dfd,stroke:#333,stroke-width:2px
