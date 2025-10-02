## 1\. The Components You Need

We'll use a **5-Droplet setup** to host the scoring platform and three different, ready-made vulnerable challenges.

| Droplet Role | Purpose | Droplet Size (DO Basic) | Ready-Made Solution |
| :--- | :--- | :--- | :--- |
| **1. CTF Scoring Hub** | Hosts the CTFd scoring platform. | 2 GiB / 2 vCPUs | **CTFd Docker Compose** |
| **2. Target: Web App** | Hosts a complex, vulnerable web application. | 1 GiB / 1 vCPU | **OWASP Juice Shop** |
| **3. Target: Network A** | Hosts a vulnerable server with network challenges. | 1 GiB / 1 vCPU | **OWASP Mutillidae II** |
| **4. Target: Network B** | Hosts a simple, vulnerable service (for a quick exploit). | 1 GiB / 1 vCPU | **Vulnerable Redis or FTP Image** |
| **5. The Builder** | (Temporary) Used to prepare images and take a snapshot. | 1 GiB / 1 vCPU | N/A |

-----

## 2\. Preparation: Creating the Master Image (The Builder)

Instead of setting up four separate Droplets manually, you'll create one "Builder" Droplet and clone it.

1.  **Launch the Builder Droplet:** Create one **1 GiB / 1 vCPU Linux Droplet (Ubuntu 22.04).**

2.  **Install Essential Tools:** Connect via SSH and install Docker and Docker Compose:

    ```bash
    sudo apt update
    sudo apt install docker.io docker-compose -y
    sudo systemctl start docker
    sudo systemctl enable docker
    ```

3.  **Install CTFd (on the Builder temporarily):** While you'll move CTFd to its own hub, setting it up here first ensures the basic Docker networking works.

    ```bash
    git clone https://github.com/CTFd/CTFd.git
    cd CTFd
    docker-compose up -d # Run it once to ensure all dependencies download
    docker-compose down # Stop it immediately
    cd ..
    ```

4.  **Install Vulnerable Targets:** Pull the Docker images for your target Droplets.

      * **Juice Shop (Web App):** `docker pull bkimminich/juice-shop`
      * **Mutillidae (Network/PHP):** `docker pull citizenstig/mutillidae`
      * **Vulnerable Service (Network B):** `docker pull vulnerables/cve-2017-7657-jetty-default` (Example: a specific CVE)

5.  **Snapshot:** **CRITICAL:** Power off the Builder Droplet (`sudo shutdown now`). Then, in the DigitalOcean panel, create a **Snapshot** of this Droplet.

-----

## 3\. Deployment: Launching the CTF Infrastructure

Now you will deploy all the necessary Droplets from your prepared Snapshot into a single **VPC Network**.

1.  **Create VPC Network:** In the DigitalOcean panel, create a new **VPC Network** in your chosen region.

2.  **Launch Droplets:** Launch four new Droplets, ensuring you select your custom Snapshot and place them all in the **same VPC Network**.

      * **Droplet 1:** (2 GiB/2 vCPU) -\> Rename to **`CTF-SCORE-HUB`**
      * **Droplet 2:** (1 GiB/1 vCPU) -\> Rename to **`TARGET-WEB-APP`**
      * **Droplet 3:** (1 GiB/1 vCPU) -\> Rename to **`TARGET-NETWORK-A`**
      * **Droplet 4:** (1 GiB/1 vCPU) -\> Rename to **`TARGET-NETWORK-B`**

3.  **Configure Services:** Connect via SSH to each Droplet and run the appropriate commands:

    | Droplet | Action | Command to Run |
    | :--- | :--- | :--- |
    | **CTF-SCORE-HUB** | Start CTFd Scoring Engine | `cd CTFd && docker-compose up -d` |
    | **TARGET-WEB-APP** | Start Juice Shop (Web) | `docker run -d -p 80:3000 bkimminich/juice-shop` |
    | **TARGET-NETWORK-A** | Start Mutillidae (PHP App) | `docker run -d -p 80:80 citizenstig/mutillidae` |
    | **TARGET-NETWORK-B** | Start Vulnerable Service | `docker run -d -p 2222:8080 vulnerables/cve-2017-7657-jetty-default` |

    *Note: You may need to adjust the port numbers or Docker run commands based on the specific challenge images you select.*

-----

## 4\. Security and Access (Cloud Firewall)

Use the **DigitalOcean Cloud Firewall** to secure your environment. Apply these rules to a single firewall policy and assign it to all four Droplets.

| Protocol | Port | Source | Purpose |
| :--- | :--- | :--- | :--- |
| **TCP** | 22 | **Admin IPs Only** | Your SSH access (CRITICAL). |
| **TCP** | 80, 443 | All IPs | Public access for scoring and targets. |
| **TCP** | 2222 | All IPs | Public access for Network B target. |

-----

## 5\. Teardown (The Cost Saver)

You only have **3 hours** to run the competition. To ensure you are only billed for the absolute minimum, you must act immediately after the competition ends.

  * **Action:** In the DigitalOcean panel, select all four live Droplets and choose the **Destroy** action.
  * **Result:** Your total running cost will be approximately **$0.17 USD** plus any small charges for the temporary Builder Snapshot.

