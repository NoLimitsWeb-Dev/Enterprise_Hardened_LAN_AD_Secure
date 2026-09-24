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
### 🛠️ Step 1: Create the Physical Topology
1. Open Cisco Packet Tracer.
2. 1 Server-PT (This will be our Active Directory, DNS, DHCP, and Web server).
3. 1 Switch (Select the Cisco Catalyst 2960).
4. 3 PC-PTs (I will start with 3 workstations to test our automation, then scale up).
5. 1 Printer-PT (Our centralized office network asset).

🔌 Step 2: Cable the Network (Crucial Port Mappings)

Grab the solid black Copper Straight-Through cable tool. To avoid the port confusion, I am going to manually plug each wire into a specific, designated port on the switch.

---

### ⏳ Step 3: Let the Ports Turn Green
Once everything is plugged in, look at your network diagram. You will notice the lights near the switch are blinking orange/amber.
* Look at the bottom-left toolbar of your main Packet Tracer screen.
* Click the Fast Forward Time (>>) button twice.
* Every single active cable light on your screen should now be solid green.Our physical wiring matrix is now complete and verified!
<img width="999" height="1001" alt="image" src="https://github.com/user-attachments/assets/1ee665a2-192a-46a2-bf6b-0bc1218883b5" />

---

### 🌐 Step 4: Configure IP Addresses
To make the network functional, we need to establish static IP routing.

---
|  Device  |  IP Address  |  Subnet Mask  |  Default Gateway  |
|  :---  |  :---  |  :---  |  :---  |
|  Server  |  192.168.1.10  |  255.255.255.0  |  192.168.1.1  |
|  PC0  |  192.168.1.20  |  255.255.255.0  |  192.168.1.1  |
---

### 🖥️ Step 5: Configure the Server IP
1. Click on your Server -> Go to the Desktop tab -> Open IP Configuration.
2. Fill in these static numbers exactly:
   1. IP Address: 192.168.1.10
   2. Subnet Mask: 255.255.255.0
   3. Default Gateway: 192.168.1.1
   4. DNS Server: 192.168.1.10 (It points to itself since it will host DNS).
3. Close the IP configuration box.
<img width="750" height="985" alt="image" src="https://github.com/user-attachments/assets/9aabc800-24b0-4581-a4b8-4469b827d076" />

---

### ⌨️ Step 5: Set Up the Switch Hostname and Management IP
Now let's program the switch so it has an identity and a network address that our workstations can reach.
1. Click on your Switch and navigate to the CLI tab.
2. Press Enter once to bring up the Switch> prompt.
3. Let's Copy and paste (or type) this exact clean script block:
```
enable
configure terminal
hostname Core-Switch-01
interface vlan 1
ip address 192.168.1.1 255.255.255.0
no shutdown
exit
ip default-gateway 192.168.1.1
write memory
```
<img width="758" height="465" alt="image" src="https://github.com/user-attachments/assets/e22c097b-6247-46b4-be72-12cc2d6180db" />

---
### 🧪 Step 3: Run the First Connectivity Test
Let's make sure the switch and server can speak across the wire.
1. Click on the Server -> Go to the Desktop tab -> Open the Command Prompt.T
2. ype this ping command to test the path to the switch:cmd
```
ping 192.168.1.1
```
<img width="747" height="703" alt="image" src="https://github.com/user-attachments/assets/24a7306c-ab92-4232-8108-9da3b4986725" />

*Having four successful replies back, show my infrastructure coordinates are perfect.*

---
## Phase 3:
---

### 🌐 Step 1: Turn on DHCP (IP Address Distribution)
1. Click on your Server and navigate to the Services tab at the top.
2. Select DHCP from the left-hand column.
3. Toggle the Service to ON.
4. Configure the default pool variables exactly like this:
   1. Default Gateway: 192.168.1.1
   2. DNS Server: 192.168.1.10
   3. Start IP Address: Change the last digit box to 50 (It will read: 192.168.1.50). This keeps numbers 11 through 49 safe for any future network nodes.
   4. Subnet Mask: 255.255.255.0
   5. Maximum number of Users: 50
<img width="754" height="597" alt="image" src="https://github.com/user-attachments/assets/89cbbf58-ff7e-458b-8202-1ffe6be6774b" />

5. Click the Save button directly below those entry fields.
<img width="740" height="573" alt="image" src="https://github.com/user-attachments/assets/c7e91944-1540-4841-8c33-f9d8e210cbbe" />

---

### 🔍 Step 2: Turn on Domain Name Services (DNS)
1. While still inside the Server's Services tab, select DNS from the left-hand column.
2. Toggle the Service to ON.
3. Create your domain mapping identity:
   1. Name: mycompany.local
   2. Type: A Record
   3. Address: 192.168.1.10
<img width="745" height="430" alt="image" src="https://github.com/user-attachments/assets/d4254351-6883-432a-aa9c-8068f5ec06b8" />

4. Click the Add button.
<img width="751" height="461" alt="image" src="https://github.com/user-attachments/assets/31f980cd-321b-42e0-9fc8-34b67157e905" />

---

### 🧪 Step 3: Activate the Workflow on Your Workstations
Let's test if the automated services work perfectly on your three client computers:
1. Click on PC 0 -> Go to the Desktop tab -> Open IP Configuration.
2. Change the selection setting from Static to DHCP.
3. Wait 2 seconds. You will see "DHCP request successful!" appear, and its IP will automatically populate with 192.168.1.50.
<img width="740" height="1015" alt="image" src="https://github.com/user-attachments/assets/d868ab14-faef-44bf-ad03-e7040c0ac630" />

4. Let's repeat this exact step for PC 1 and PC 2. (They will automatically receive the coordinates 192.168.1.51 and 192.168.1.52).

---
### Phase 3:
---

### 🗂️ Step 1: Configure the Switch Entry on the Server
1. Click on your Server and go to the Services tab.
2. Select AAA from the left-hand column menu.
3. Toggle the Service to ON.
4. First, let's identify your switch to the server so they can securely communicate:
   1. Client Name: Core-Switch-01
   2. Client IP: 192.168.1.1 (The exact management IP we gave the switch)
   3. Secret: cisco123 (Our shared secret communication key)
   4. Server Type: Ensure RADIUS is selected.
<img width="747" height="907" alt="image" src="https://github.com/user-attachments/assets/ff1ebb83-46dd-4d55-ade4-e3b2f112b875" />

5. Click the Add button directly under those network fields.
<img width="751" height="520" alt="image" src="https://github.com/user-attachments/assets/85a28db0-6645-4470-b464-c4a95658954f" />

---
### 👤 Step 2: Create Your Corporate User Accounts
Now let's scroll down slightly on that same screen to the User Setup section. We will add our two test employee personas:
1. **The Administrator Account:**
   1. **Username:** alex.brown
   2. **Password:** P@ssword1
   3. Click **Add**.
2. **The Restricted Support Account:**
   1. Username: customer.care
   2. Password: P@ssword1
   3. Click Add.
<img width="752" height="904" alt="image" src="https://github.com/user-attachments/assets/6c8f23f3-1d0b-47d6-810c-a0ec7d241229" />

---

### 🧪 Step 3: Configure the Network Printer for DHCP
Since our user database is ready, let's also make sure your office Printer is online before we lock down the switch security profiles.
1. Click on your Printer asset (on Port 6).
2. Go to the Config tab -> Click on FastEthernet0 on the left menu.
3. Change the IP Configuration setting from Static to DHCP.
4. Wait 2 seconds. It should automatically grab the next coordinate pool address.
<img width="737" height="902" alt="image" src="https://github.com/user-attachments/assets/7f89eed9-c051-4570-b8b7-66be72713164" />

---
### Phase 4
---

### 🖥️ Step 1: Add the Main Website Home Page
1. Click on your Server and navigate to the Services tab at the top.
2. Select HTTP from the left-hand column menu.
3. Ensure both HTTP and HTTPS toggles are set to ON.
4. Scroll down the file manager list, find index.html, and click the edit link on the right side.
5. Wipe out everything inside the file and paste this clean, simple HTML layout:
```
<html>
  <head><title>MyCompany Intranet</title></head>
  <body>
    <h1>Welcome to the MyCompany Corporate Intranet Portal!</h1>
    <h3>Authorized Employee Access Only</h3>
    <hr>
    <p><a href="printer.html">Go to Central Office Printing Dashboard</a></p>
  </body>
</html>
```
6. Click the Save button at the bottom and click Yes to overwrite the file.
<img width="736" height="907" alt="image" src="https://github.com/user-attachments/assets/0d2d5274-9f45-4f3e-b160-8f1247a11dda" />

---

### 🖨️ Step 2: Create the Printing Status Dashboard Page
Now let's build the dedicated webpage that displays your active printing network statistics.
1. While still inside the Server's HTTP file manager, click the New File link (usually located at the very top or bottom of the file repository list).
2. For the file name, type exactly: printer.html
3. Inside the blank file workspace, copy and paste this custom status dashboard layout:
```
<html>
  <head><title>Print Server Dashboard</title></head>
  <body>
    <h2>MyCompany Central Print Server</h2>
    <hr>
    <p><b>Printer Status:</b> Online & Operational</p>
    <p><b>Network Address:</b> 192.168.1.53</p>
    <p><b>Paper Level:</b> 95% (Letter Capacity)</p>
    <p><b>Toner Level:</b> 98% (Black & White Standard)</p>
    <p><b>Active Jobs in Queue:</b> 0 Jobs Pending</p>
    <br>
    <hr>
    <a href="index.html">Back to Main Corporate Portal</a>
  </body>
</html>
```
<img width="745" height="910" alt="image" src="https://github.com/user-attachments/assets/2cd2d18a-7a80-4e85-b6d5-24a25f07b2de" />

4. Click the Save button and select Yes to secure the document.
---

### 🧪 Step 3: Browse the Dashboard from Your PCs
Let's make sure the website and links load up flawlessly across your workstations.
1. Click on PC 0 (or any of your 3 PCs) -> Go to the Desktop tab -> Open the Web Browser application.
2. In the URL bar at the top, type your friendly address and hit Enter:
```
globalbank.com
```
<img width="734" height="484" alt="image" src="https://github.com/user-attachments/assets/fcf0fec4-09e1-43a2-a9eb-09908466bfa4" />

3. Your main company welcome page will load up. Click on the link that says "Go to Central Office Printing Dashboard".
4. The page will immediately update to display your paper levels, toner updates, and printer coordinate points!
<img width="750" height="648" alt="image" src="https://github.com/user-attachments/assets/4c07104f-03ea-4aeb-83ff-984f35702c58" />

---
### Phase 5:
---

### 🛠️ Step 1: Arm the Switch with AAA Protection
1. Click on Core-Switch-01 and navigate to the CLI tab.
2. Press Enter to see the command prompt.
3. Copy and paste this complete block of security rules directly into the terminal window:
```
configure terminal
service password-encryption
username admin privilege 15 secret LocalAdminPass123
enable secret CorporateAdmin789
aaa new-model
radius server AD_SERVER
address ipv4 192.168.1.10
key cisco123
exit
aaa authentication login default group radius local
aaa authorization exec default group radius local
line vty 0 4
privilege level 1
transport input all
exit
exit
write memory
```
<img width="741" height="917" alt="image" src="https://github.com/user-attachments/assets/1a536bac-c50f-44c4-9403-1c8e1bebcab0" />

---

### 🧪 Test 1: Validate the Admin Account (alex.brown)
1. In the PC Command Prompt, initiate the management link:
```
telnet 192.168.1.1
```
2. Enter the credentials exactly as saved:
   1. Username: alex.brown
   2. Password: P@ssword1
3. Notice it land on the restricted Core-Switch-01> screen. To unlock full administrative power, type:
```
enable
```
4. Enter the master supervisor key: CorporateAdmin789. Your prompt will instantly shift to Core-Switch-01#, granting full administrative configuration powers!
5. Type exit and hit Enter to log out and clear the line for the next test.
<img width="705" height="378" alt="image" src="https://github.com/user-attachments/assets/2d784c95-03a1-4909-a720-cbb89face9af" />

---
### 🧪 Test 2: Validate the Support Account (customer.care)
1. Initiate the management link once more from the PC Command Prompt:
```
telnet 192.168.1.1
```
2. Enter the support operator credentials:
   1. Username: customer.care
   2. Password: P@ssword1
3. You will land on the resting read-only view (Core-Switch-01>).
4. Attempt to run configuration changes by typing:
```
configure terminal
```
<img width="733" height="598" alt="image" src="https://github.com/user-attachments/assets/b35d8b3f-9e6d-4d74-95c1-be7a4aa6d33e" />

The switch will immediately reject the input and say Bad secrets or "% Invalid input detected" or block the transaction. This proves your role restrictions are fully working!

---

Let’s scale up my network infrastructure! I will be adding 3 new PC's and 3 new employee accounts. Testing how an environment handles growth is an excellent way to prove that my architecture can support a expanding corporate business.

Because I engineered the system with automated DHCP and centralized AAA, adding more devices and users will be rapid and seamless.
---

### 🖥️ Step 1: Scale the Hardware Layout (Add 3 More PCs)
Let's expand my corporate office floor from 3 workstations to 6.
1. Go to your bottom-left device library, select End Devices, and drag 3 more PC-PTs onto your workspace (they will likely be named PC3, PC4, and PC5).
2. Grab the solid black Copper Straight-Through cable.
3. Manually map each new computer to an empty port on your switch:
   1.   Connect PC 3 (FastEthernet0) ➡️ Switch (FastEthernet0/5)
   2.   Connect PC 4 (FastEthernet0) ➡️ Switch (FastEthernet0/7)
   3.   Connect PC 5 (FastEthernet0) ➡️ Switch (FastEthernet0/8)
4. Click on each of the 3 new PCs (PC3, PC4, PC5), go to Desktop -> IP Configuration, and click DHCP.
---

### 🗂️ Step 2: Scale the Identity Registry (Add More Employee Roles)
Let's expand your Active Directory database by adding an executive account and an engineering profile.
1. Click on your Server and navigate to the Services tab -> AAA menu.
2. Ensure the service toggle remains ON, scroll down to the User Setup ledger, and add these new operational identities:
   * The Chief Technology Officer (Full Admin privileges):
     * Username: jane.smith
     * Password: SecureCto789!
     * Click Add.
   * Head of Marketing (Restricted operator status):
     * Username: head.marketing 
     * Password: Market551
     * Click Add.
<img width="762" height="400" alt="image" src="https://github.com/user-attachments/assets/c5c361d5-b4c5-4d0d-88da-48675d7c56a2" />

---

### 🧪 Step 3: Run a Verification Scan from the New Tier
Let's ensure your scaled environment can securely touch the infrastructure management lines:
1. Click on your newly added workstation PC 4.
2. Go to the Desktop tab and launch the Command Prompt.
3. Attempt to establish an administration terminal connection back to the switch:
```
telnet 192.168.1.1
```
4. Authenticate using your brand-new user identity: jane.smith with password SecureCto789!.
