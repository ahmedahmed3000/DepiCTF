That's the ideal way to practice—a full penetration test scenario that forces you to pivot through multiple stages!

Since you want one machine to cover **Recon**, **Networks**, **Authentication**, **Authorization**, **Command Injection**, **File Upload**, and **Linux Privilege Escalation** in a 3-hour window, you need a classic, well-regarded, and not overly complex **"Boot-to-Root"** style virtual machine.

The best source for a downloadable, standalone VM like this is **VulnHub**.

---

## Recommended Free Standalone CTF Machine

The most classic and perfectly-scoped machine for your team's goal is:

### VulnHub: **"Basic Pentesting 1"**

This virtual machine is designed specifically for beginners/intermediate teams and is consistently recommended because it has multiple paths and a straightforward Linux privilege escalation phase. It hits almost every category you listed in a linear progression.

### **How it Addresses Your Requirements:**

| Skill Area | How Basic Pentesting 1 Covers It |
| :--- | :--- |
| **Recon & Networks** | Requires **Nmap** scanning to find open ports (SSH, FTP, HTTP) and services, a key first step. |
| **Authentication/Authorization** | You'll likely discover credentials or a login page and need to attempt to bypass or guess them. |
| **Command Injection / File Upload** | While there are multiple entry points, the intended web app path often involves finding a vulnerability like **command injection** or a **file upload** flaw to get your initial shell. |
| **Privilege Escalation (Linux)** | Once you have a low-privilege shell, you'll need to enumerate the Linux system (check for SUID, kernel exploits, cron jobs, etc.) to gain root. |

### **How to Use It with Your Team:**

1.  **Download:** Go to the VulnHub website and search for **"Basic Pentesting 1"** (or use similar, highly-rated *easy* VMs like **"FristiKid's: V1.01"**). Download the VM file (usually a `.ova` or `.vmdk`).
2.  **Setup:** Import the VM into your team's virtualization software (**VirtualBox** or **VMware**) and ensure it's on a **host-only** or **NAT network** so it's isolated from your home/work network.
3.  **Start the Clock:** Your team's 3-hour timer starts!
4.  **Team Strategy:**
    * **Pivoter/Recon:** One person focuses on network scanning and directory enumeration.
    * **Web Tester:** One person focuses on the HTTP/FTP services, testing the web application for injection, file upload, and auth flaws.
    * **PrivEsc Specialist:** The rest of the team prepares enumeration scripts and looks for post-exploitation tools, ready to take over once the initial shell is achieved.

Using a VulnHub machine is perfect because it's **free**, **downloadable**, and provides a **full-system challenge** where you must chain multiple exploit types together.
