# DepiCTF
NetworkCTF

I cannot directly create or upload a PDF file. However, I can generate the complete, formatted content for your CTF infrastructure plan, which you can easily **copy and paste into any document editor (like Google Docs, Microsoft Word, or LaTeX)** and then save or print as a PDF.

Here is the complete content, structured and ready for your CTF event:

---

# CTF Infrastructure Plan: AQUILA Cyber Security Audit

## I. Overview and Event Scope

This plan outlines a highly cost-effective, **6-hour Capture The Flag (CTF)** competition hosted on **DigitalOcean**, supporting approximately **30 competitors**. The infrastructure uses a minimalist approach with **one Linux OS (Ubuntu)** deployed across all virtual machines and a single **Virtual Private Cloud (VPC)** subnet.

| Constraint | Solution |
| :--- | :--- |
| **Duration** | 6 Hours |
| **Competitors** | $\approx 30$ |
| **Operating System** | Linux (Ubuntu) only |
| **Network** | Single VPC Subnet (No dedicated Firewall Droplet) |
| **Core Vulnerabilities** | Authentication (AuthN), Authorization (AuthZ), Command Injection, Reconnaissance, File Upload |

---

## II. The Scenario: AQUILA Cyber Security Audit

**The Narrative:** Competitors are penetration testers hired to audit the internal systems of AQUILA, a risk management firm. The challenge involves breaching a public-facing web application and pivoting to exploit legacy network services to find the final flags.

| Droplet Role | Accessibility | Primary Vulnerabilities |
| :--- | :--- | :--- |
| **1. CTF Scoring Hub** | Public IP (HTTPS: 443) | Host the CTFd scoring platform. |
| **2. Target: Web Portal** | Public IP (HTTP: 80) | **AuthN, AuthZ, File Upload** |
| **3. Target: Network A** | Public IP (Custom Port: 9001) | **Recon, Command Injection** |
| **4. Target: Network B** | Public IP (Custom Port: 2222) | **Version Exploit, Authentication** |

---

## III. Infrastructure and Costing (6-Hour Estimate)

All Droplets are **Basic Shared CPU** for minimum cost. Note that DigitalOcean bills hourly, capped at the monthly price. **Teardown is critical.**

| Droplet Name | Specs (Suggested) | Monthly Price | Estimated 6hr Cost |
| :--- | :--- | :--- | :--- |
| **CTF-SCORE-HUB** | 2 GiB Memory / 2 vCPUs | $18.00 | $\approx \$0.16$ |
| **Target-WEB-PORTAL** | 1 GiB Memory / 1 vCPU | $6.00$ | $\approx \$0.05$ |
| **Target-NETWORK-A** | 1 GiB Memory / 1 vCPU | $6.00$ | $\approx \$0.05$ |
| **Target-NETWORK-B** | 1 GiB Memory / 1 vCPU | $6.00$ | $\approx \$0.05$ |
| **Total Estimated Cost for 6 Hours** | | | **$\approx \$0.31$** |

### Deployment Strategy (Linux & Docker)

1.  **Image Preparation:** Build one master Ubuntu Droplet. Install **Docker** and **Docker Compose**. Configure all challenge services (web app, network services) to run as containers on this Droplet.
2.  **Snapshot:** **Power Off** the master Droplet and create a **Snapshot**.
3.  **Launch:** Use the Snapshot to deploy the three **Target** Droplets. Deploy the **CTF-SCORE-HUB** Droplet separately.

---

## IV. Network and Security Configuration

### A. Network (Single Subnet)

* All four Droplets must be provisioned within the **same Virtual Private Cloud (VPC) network**.
* All challenges are accessed via their individual **Public IP** addresses.

### B. Access Control (DigitalOcean Cloud Firewalls)

Since there is no dedicated firewall Droplet, the **DigitalOcean Cloud Firewall** service must be used to restrict access to each Droplet based on its role.

| Droplet | Service | Port | Source IP | Access Type |
| :--- | :--- | :--- | :--- | :--- |
| **ALL Droplets** | SSH (Admin Access) | TCP 22 | **Admin IPs Only** | **Admin Access (CRITICAL)** |
| **CTF-SCORE-HUB** | HTTPS | TCP 443 | All IPs | Public |
| **Target-WEB-PORTAL** | HTTP | TCP 80 | All IPs | Public |
| **Target-NETWORK-A** | Custom Service | TCP 9001 | All IPs | Public |
| **Target-NETWORK-B** | Custom Service | TCP 2222 | All IPs | Public |

---

## V. Detailed Challenge Vulnerabilities

### 1. Target: Web Portal (HTTP: 80)

* **Recon & Authentication (AuthN):** A hidden comment or a leaked file (like `robots.txt`) exposes a weak default username, such as `auditor`. Competitors must use simple password guessing or weak session handling to gain initial access.
* **Authorization (AuthZ):** The low-privilege `auditor` user gains access to a hidden administration function by modifying a URL parameter or a cookie value (e.g., an IDOR attack or parameter tampering).
* **File Upload (FU) & Command Injection:** The secret admin page includes a feature to upload a "new report" or "profile picture." This feature has weak file extension validation, allowing an attacker to upload a reverse shell script (e.g., a `.php` or Python script) and execute it to gain a shell on the underlying Droplet.

### 2. Target: Network A (TCP: 9001)

* **Recon (Clue-Gathering):** Upon connecting with `netcat`, the service banner leaks a developer's name: "AQUILA Logging Service v1.0. For support, contact **'J.Doe'**." This name is a crucial clue for the next challenge.
* **Command Injection (CMD Inj):** The service prompts for a "Log Identifier." The input is concatenated directly into a system command (`subprocess.run('grep ' + user_input, shell=True)`) without sanitization. Competitors exploit this using shell metacharacters (e.g., `||`, `;`) to run arbitrary system commands and read the flag file.

### 3. Target: Network B (TCP: 2222)

* **Port/Version Recon:** Competitors must use **Nmap** to accurately identify the specific, vulnerable version of the legacy network service (e.g., an old version of ProFTPd, vsftpd, or a custom unpatched binary).
* **Authentication (AuthN Bypass):** The service either has a known public exploit (CVE) tied to its version that bypasses login, or it accepts the weak password associated with the developer name **'J.Doe'** discovered in the previous challenge.
* **Flag Retrieval:** Successful exploitation or authentication grants shell access or direct file read access to the final flag.

---

## VI. Critical Post-Event Action

To ensure you are only billed for the actual 6-hour usage, you must take the following step immediately after the competition ends:

* **Destroy All Droplets:** Navigate to the DigitalOcean Control Panel, select all four Droplets, and use the **Destroy** action. **Powering off a Droplet is not enough; it must be destroyed.**
