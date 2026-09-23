## automated-enterprise-network-simulation
Scalable branch office architecture featuring central AAA/RADIUS, DHCP distribution pools, and DNS web infrastructure.
---

## Secure Enterprise LAN Infrastructure & Hardening Simulation

### 📌 Project Overview
This repository contains the complete design, deployment blueprint, and security hardening lifecycle of an enterprise-grade branch office Local Area Network (LAN). Built entirely within Cisco Packet Tracer, this project demonstrates structural defense-in-depth methodologies by integrating a centralized Active Directory / AAA framework with robust Layer-2 network security mechanisms to actively mitigate both physical intrusion and sophisticated insider threat vectors.
---

### 🎯 Core Engineering Objectives
* **Centralize Identity Governance:** Offload device authentication parameters onto a centralized directory framework using the RADIUS standard protocol.
* **Automate Infrastructure Scaling:** Eliminate manual client configuration by standing up dynamic IP allocation arrays.
* **Implement Least Privilege Execution:** Define deterministic security checkpoints using Role-Based Access Control (RBAC).
* **Minimize Internal Attack Surfaces:** Deploy edge security controls at the physical port level and completely lock out unassigned building outlets.
* **Demonstrate Defensive Validation:** Replicate a Man-in-the-Middle (MitM) credential harvesting loop via traffic mirroring to prove the critical necessity of cryptographic protocols (SSH v2).

### 🗺️ Architectural Network Topology Blueprint
<img width="1913" height="1020" alt="image" src="https://github.com/user-attachments/assets/3f32fbb9-b6d4-4e3c-aeed-c60fe7e4109f" />

---

|  Node Identity  |  Port Binding  |  IP Coordinates  |  Role & Operational Layer Profile  |
|  :---  |  :---  |  :---  |  :---  |
|  Active Directory Services  |  Fa0/2  |  192.168.1.10 (Static)  |AAA/RADIUS Datastore, DHCP Scope Pool, Domain Name Server (DNS), and Corporate Intranet Web Host.  |
|  Core Switching Element  |  VLAN 1  |  192.168.1.1 (Static)  |  Core-Switch-01 (Cisco Catalyst 2960). Enforces localized Layer-2 boundaries, port protection profiles, and VTY ingress paths.  |
|  Corporate Workstations  |  Fa0/1 – Fa0/5, Fa0/7  |  Dynamic Leasing  |  Six employee endpoint assets automatically configured with standard gateway, subnet, and nameserver parameters.  |
|  Shared Network Printer  |  Fa0/9  |  Dynamic Leasing  |  Swapped tracking interface managed remotely via an interactive central printer status web dashboard (printer.html).  |
|  Diagnostic Sniffer Node  |  Fa0/11  |  Ingress Monitor  |  Dedicated network analysis tap mapped as the target line for automated traffic mirroring (SPAN).  |
|  Malicious Actor Node  |  Fa0/6 / Fa0/10  |  Attack Target  |  Rogue adversarial laptop executing physical layer data harvesting and network perimeter sniffing.  |
---

### 🚨 Executed Attack & Defense Scenarios
Phase 1: Automated Network Provisioning & Domain Hosting
* The Environment: Deployed a core server managing DHCP distribution (allocating IPs from .50 to .100 to safeguard fixed internal infrastructure) and DNS parameters mapping mycompany.local to the corporate web server. Employees access an internal intranet web portal housing a central Office Printing Management Monitor Dashboard.
---

### 🛠️ Step 1: Create the Physical Topology
1. Open Cisco Packet Tracer.
2. Drag and drop the following devices onto your workspace:
   * 1 Server-PT  and name AD_Server (This will act as our simulated "Active Directory / AAA" Server)
   * 1 Switch (e.g., Cisco Catalyst 2960)
   * 1 PC-PT or Laptop-PT  (To test user authentication)
3. Connect the Server and the PC to the Switch using standard Copper Straight-Through cables.
<img width="946" height="1031" alt="image" src="https://github.com/user-attachments/assets/a3d10beb-d62e-41ba-8310-799fed0a8d0c" />

---

### 🌐 Step 2: Configure IP Addresses
To make the network functional, we need to establish static IP routing.

---
|  Device  |  IP Address  |  Subnet Mask  |  Default Gateway  |
|  :---  |  :---  |  :---  |  :---  |
|  Server  |  192.168.1.10  |  255.255.255.0  |  192.168.1.1  |
|  PC  |  192.168.1.20  |  255.255.255.0  |  192.168.1.1  |
---

1. Click on the Server → Go to the Desktop tab → Open IP Configuration.
2. Fill in the values using the table above. Ensure you also set the DNS Server on the server itself to 192.168.1.10.
<img width="742" height="656" alt="image" src="https://github.com/user-attachments/assets/8bbecbe5-0dd8-4a63-9da9-81097e428d31" />

3. Repeat the process for the PC, ensuring its DNS Server is also set to 192.168.1.10.
<img width="734" height="750" alt="image" src="https://github.com/user-attachments/assets/be9d0edd-d3a2-4437-9449-12a6341edb74" />

---

### 🖥️ Step 3: Configure the Simulated AD Services
1. Setup Domain Name Resolution (DNS)

Real Active Directory environments rely heavily on DNS.
1. Click on the Server and navigate to the Services tab.
2. Click DNS on the left menu.
3. Turn the service ON.
4. Create a record for your domain:
   * Name: branch.com
   * Type: A Record
   * Address: 192.168.1.10
<img width="750" height="400" alt="image" src="https://github.com/user-attachments/assets/4c2ab227-8e53-4926-abfe-19c99daa5cd7" />

5. Click Add.
<img width="738" height="434" alt="image" src="https://github.com/user-attachments/assets/f9214ff3-4e4d-466f-b7b4-397940e71150" />

2. Create the User Database (AAA / RADIUS)

Instead of an AD database, Packet Tracer uses AAA (Authentication, Authorization, and Accounting) to centrally manage user credentials.
   1. While still in the Services tab of the Server, click on AAA.
   2. Turn the service ON.
   3. Configure the Network Client (the device requesting authentication, such as a switch or a router):
      * Client Name: Switch1
      * Client IP: 192.168.1.1
      * Secret: cisco123 (this is the shared secret key)
      * Server Type: Select RADIUS
 <img width="752" height="1010" alt="image" src="https://github.com/user-attachments/assets/16a386b6-8577-4022-a98e-2c5e0e8230d1" />

  4. Click Add.
<img width="751" height="531" alt="image" src="https://github.com/user-attachments/assets/048ef1ab-afc4-4a95-9dd4-85879a8874b7" />

  5. Scroll down to the User Setup section to create simulated Active Directory users:
     * Username: BM_PC
     * Password: P@ssword1
<img width="748" height="999" alt="image" src="https://github.com/user-attachments/assets/a2a158e2-f973-4a15-962e-bfa1bd513870" />

  6. Click Add (repeat this step for as many employee accounts as you want to simulate).
<img width="740" height="488" alt="image" src="https://github.com/user-attachments/assets/147724a6-bad5-49ea-81ea-48191e69b77a" />

  7. Let's add five (5) more employee accounts now.
<img width="740" height="987" alt="image" src="https://github.com/user-attachments/assets/db14ab3e-109c-4ba9-9bda-e9ae5e301321" />

---
### 🧪 Step 4: Testing Your Setup
While you can't join the PC to a Windows Domain via the typical Windows GUI interface in Packet Tracer, you can test if the network-wide credential validation works:
1. Click on the PC.
2. Go to the Desktop tab and open the Command Prompt.
3. Run a test ping to verify basic connectivity: 
```
ping 192.168.1.10
```
<img width="749" height="408" alt="image" src="https://github.com/user-attachments/assets/ddeae4fb-0142-4102-bc2b-bd27928d3ad2" />

---
