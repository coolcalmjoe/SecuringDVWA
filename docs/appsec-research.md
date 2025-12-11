# Vulnerability Management & AppSec Research

## 1. The "Grey Box" Analysis Workflow
This lab allows for a comprehensive analysis loop. I can attack the application, see the WAF logs, detect the runtime execution in OpenRASP, and step through the code with Xdebug.

**The Loop:**
1.  **Attack:** OWASP ZAP sends a malicious payload (e.g., SQL Injection).
2.  **Detect:** OpenRASP (IAST) triggers an alert inside the PHP runtime.
3.  **Analyze:**
    * **Logs:** Viewed via `lnav` on the host.
    * **Debug:** I connect VS Code to the remote container using a **Reverse SSH Tunnel** (`ssh -R`) to hit breakpoints on the specific line of code.
4.  **Remediate:** The fix is applied, and the pipeline verifies the closure.

## 2. Research Capabilities
The `dvwa-app` container is built with a custom "Lab Mode" toggle.

* **Xdebug (v3.1.6):** Configured for Remote Debugging on port `9003`.
* **OpenRASP (v1.3.7):** A Runtime Application Self-Protection agent from Baidu.
* **Lab Mode Script:** A custom script `/usr/local/bin/lab-mode-on` renames `.disabled` config files to `.ini` and reloads Apache to enable instrumentation on the fly.

## 3. Vulnerability Data Integration
All scan data is centralized in **DefectDojo**.

* **SAST:** Semgrep findings (Code-level).
* **DAST:** ZAP findings (Runtime-level).
* **SCA:** Dependency-Track (Library-level).
* **Container:** Trivy (OS-level).

*See the `/reports` folder in this repository for sample PDF exports of these scans.*
