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

|  **Node Identity**  |  **Port Binding**  |  **IP Coordinates**  |  **Role & Operational Layer Profile**  |
|  :---  |  :---  |  :---  |  :---  |
|  **Active Directory Services**  |  Fa0/2  |  192.168.1.10 (Static)  |AAA/RADIUS Datastore, DHCP Scope Pool, Domain Name Server (DNS), and Corporate Intranet Web Host.  |
|  **Core Switching Element**  |  VLAN 1  |  192.168.1.1 (Static)  |  Core-Switch-01 (Cisco Catalyst 2960). Enforces localized Layer-2 boundaries, port protection profiles, and VTY ingress paths.  |
|  **Corporate Workstations**  |  Fa0/1 – Fa0/5, Fa0/7  |  Dynamic Leasing  |  Six employee endpoint assets automatically configured with standard gateway, subnet, and nameserver parameters.  |
|  **Shared Network Printer**  |  Fa0/9  |  Dynamic Leasing  |  Swapped tracking interface managed remotely via an interactive central printer status web dashboard (printer.html).  |
|  **Diagnostic Sniffer Node**  |  Fa0/11  |  Ingress Monitor  |  Dedicated network analysis tap mapped as the target line for automated traffic mirroring (SPAN).  |
|  **Malicious Actor Node**  |  Fa0/6 / Fa0/10  |  Attack Target  |  Rogue adversarial laptop executing physical layer data harvesting and network perimeter sniffing.  |
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

---

### 🔑 How to Unlock Admin Mode for Jane
Whenever an administrator wants to make changes, the switch requires the master supervisor password.
1. At that blank Password: line, type your master switch code:
```
CorporateAdmin789
```

Press Enter. (Remember, the letters will not appear on the screen as you type them).

Your prompt will instantly change to Core-Switch-01#, granting Jane Smith full administrative access to change configurations!

---

### Phase 6:
---
# Scenario 1:

Global Bank just did renovation of her newly acquired building. During the renovation the IT department felt it was better to trunk and terminate all cables directly into an internet faceplate.

A threat actor (Hacker) discovered a vulnerability:
1. DHCP configuration of the IP system of the bank.
---
### 🛡️ Next Milestone: Let's Run the Port Security Attack!
Now that my network has successfully scaled up to 6 workstations, has 4 distinct user accounts, and is completely stable, my infrastructure is perfectly prepared for the final security challenge.

Let's simulate the physical cyber attack on your printer port (Port 6) to make sure your defensive configurations drop the intruder instantly.

### 🚨 Step 1: Perform the Physical Cable Hijack
1. Unplug the network cable directly out of your corporate Printer.
2. Plug that exact same cable straight into your Hacker_Laptop.
3. (The link light on the wire might look green or amber initially as Packet Tracer establishes a basic hardware connection).
<img width="955" height="997" alt="image" src="https://github.com/user-attachments/assets/f874bbf1-1121-4c44-8871-a3606a062af4" />

---
### 💥 Step 2: Fire the Malicious PacketFor the switch to catch the intruder, the hacker laptop must transmit data down the wire so the switch can inspect its hardware fingerprint.
1. Click on the Hacker_Laptop and navigate to the Desktop tab.
2. Open the Command Prompt application.
3. Type the attack command to generate traffic toward your private network server:
```
ping 192.168.1.10
```
4. Press Enter and instantly look back at your network topology map.
<img width="742" height="457" alt="image" src="https://github.com/user-attachments/assets/926edf55-e04b-41fe-ab04-e525833877e0" />

---

Now, we are going to set the trap. We are going to turn port security back on right now while the hacker is still plugged into Port 6. We will tell the switch to dynamically memorize the very next device that talks. Right now, that's the hacker.

But then, we will swap the cable back to the legitimate Printer and watch the printer trigger the shutdown! This is a great way to see the switch catch an anomaly because the switch will think the printer is the "intruder" since it learned the hacker first.

---

### 🛠️ Step 1: Arm the Trap on Port 6
Click on your Switch, open the CLI tab, and enter these commands exactly to turn security back on:
```
configure terminal
interface fastethernet 0/6
switchport mode access
switchport port-security
switchport port-security maximum 1
switchport port-security mac-address sticky
switchport port-security violation shutdown
exit
exit
write memory
```
<img width="726" height="903" alt="image" src="https://github.com/user-attachments/assets/369656b9-15ec-4595-b81a-f39be35c6f03" />

---

### 💾 Step 2: Force the Switch to Memorize the Hacker
Right now, the switch's security table is empty again. Let's make the switch lock onto the hacker laptop's hardware fingerprint first.
1. Click on your Hacker_Laptop and open the Command Prompt.
2. Run a ping to the server to force data down the wire:
```
ping 192.168.1.10
```
3. The ping will succeed, but look behind the scenes! If you check your Switch CLI by running show port-security address, you will see that the switch has officially memorized the Hacker_Laptop's MAC address as the "safe" owner of Port 6.
<img width="730" height="267" alt="image" src="https://github.com/user-attachments/assets/7253650c-8afd-43b8-bfd8-ef259427cfe4" />

---

The switch has successfully memorised the MAC address 000C.CFCD.5032 on port Fa0/6. Because the hacker laptop was the active device that sent the last ping, that unique signature belongs to the hacker laptop! The switch now firmly believes that the hacker is the only trusted device allowed on that port.

Now, we are going to spring the trap by plugging the Printer back into that wire. Since the printer has a completely different MAC address, the switch will flag it as an unauthorized intruder and lock down the line.

---

<img width="1123" height="1000" alt="image" src="https://github.com/user-attachments/assets/d6589e21-e21f-4935-a894-979a4f4f5fee" />

### 🚨 Step 3: Perform the Reverse Attack (The Trap Springs!)
Now, let's pull the rug out from under the system.
1. Unplug the cable from the Hacker Laptop.
2. Plug it straight back into your corporate Printer.
3. Now, we need the printer to talk. Click on your Printer -> Go to the Config tab -> FastEthernet0 interface.
4. Toggle the IP configuration from DHCP to Static, and then right back to DHCP to force it to transmit data packets.
<img width="746" height="483" alt="image" src="https://github.com/user-attachments/assets/ecec657b-7898-4d99-ba0b-bc80b4291c33" />


### 🔍 Watch the Link Light!
<img width="1123" height="1016" alt="image" src="https://github.com/user-attachments/assets/b2bbb814-96d5-40b7-bbcf-19502f777b72" />

The exact millisecond the printer attempts to broadcast its hardware footprint to renew its IP address, the switch will compare it to the hacker's address it memorized in Step 2. It will realize a hardware mismatch has occurred, slam the port shut, and the link light will instantly snap to solid RED!

---

### Scenario: The Unsecured Switch Port Vulnerability
1. The Incident & TroubleshootingThe ICT Department received a critical helpdesk ticket reporting that a network printer was completely unresponsive and failing to print. An IT representative was dispatched to investigate.

After several hours of extensive troubleshooting—including restarting the print spooler, checking drivers, and cycling the power—the technician decided to test the physical layer.  He unplugged the printer's network cable from the dead interface and plugged it into Switch Port 9 (from its original wall jack and plugged it into a different, adjacent network port on the wall).

Immediately, the printer pulled a new connection and started printing successfully. The issue was resolved for the user, and the technician closed the ticket.

### 2. The Security Blind Spot (The Hacker's Discovery)
Unbeknownst to the IT representative, the successful troubleshooting step revealed a major security flaw. A hacker conducting internal reconnaissance on the network noticed the same thing: multiple unused network ports across the office were fully active and patched directly into the core switch.

The hacker discovered that they could simply plug a rogue laptop into any vacant wall port or change their connection to another open switch port. So the hacker plugged a rogue laptop into Port 10 and instantly gained unrestricted access to the internal network.  Because these ports were left open and unmonitored, the hacker gained unrestricted access to the internal network, allowing them to bypass physical security boundaries and begin sniffing network traffic.

### 3. The Solution: Hardening the Network Switch

To remediate this vulnerability, the ICT department must implement a strict port-security policy. While Port 9 remains enabled for the printer, all other unused interfaces—including the exploited Port 10—must be administratively shut down.

Here is the configuration to secure the switch (assuming a standard 24-port Cisco switch):
```
! Access the switch configuration mode
Switch# configure terminal

! Secure Port 9 specifically for the printer (Optional: bind it to the printer's MAC)
Switch(config)# interface fastEthernet 0/9
Switch(config-if)# description Network_Printer_Port
Switch(config-if)# switchport mode access
Switch(config-if)# switchport port-security
Switch(config-if)# switchport port-security maximum 1
Switch(config-if)# switchport port-security violation shutdown

! Administratively shut down Port 10 and all other unused ports
Switch(config)# interface range fa0/10 - 24
Switch(config-if-range)# shutdown
Switch(config-if-range)# description Unused_Port_Disabled_by_ICT

! Exit and save the changes
Switch(config-if-range)# end
Switch# write memory
```

### 🔍 Check the Impact on Your Topology
The exact millisecond you execute those commands:
1. Look directly at your Hacker_Laptop connected to Port 10. Its connection light will instantly change from green to solid RED.
2. The hacker's terminal session is permanently disconnected. If they physically move their wire to Port 11, 12, or 24, those wall jacks are completely dead because the software has disabled them.
3. Look at your Printer on Port 9 and your 6 PCs on Ports 1-8. They remain completely untouched, green, healthy, and operational!

This completes the baseline defensive design for an enterprise network: Only authorized corporate endpoints have open ports, and the rest of the building is completely locked down.

<img width="1143" height="1023" alt="image" src="https://github.com/user-attachments/assets/e4cc4cb1-f0c4-495d-9ddb-dbefdc535836" />

Best Practices Implemented
* **Network Hardening:** Unused ports no longer provide an open invitation to attackers.
* **Change Control:** If a user needs a new port activated in the future, it must go through an official ICT request, ensuring full visibility of all connected devices.

---

### Scenario: The Malicious Insider & Network Sniffer
A senior systems administrator, passed over for a major promotion, decides to monetize corporate intellectual property by capturing unencrypted traffic directly from the local area network (LAN). Using his elevated administrative privileges, they install a software-based packet sniffer (such as Wireshark or tcpdump) on a core staging server. Because the company's internal database replication traffic is natively unencrypted, the admin successfully captures proprietary source code, customer records, and corporate strategy files passing over the wire. They then quietly exfiltrate these PCAP (packet capture) files to a personal cloud storage account and sell them directly to the company's primary market competitor.

This is a classic Insider Threat and Data Exfiltration blueprint. It perfectly illustrates why organizations cannot rely on perimeter defenses alone—if a user already holds legitimate administrative access, they can manipulate internal routing and monitoring systems from the inside.

Let's build this scenario directly into my Packet Tracer lab using a dedicated database staging server to model how this data harvest occurs and how to mathematically neutralize it.
---

### 🗄️ Step 1: Deploy the Database Staging Infrastructure
To isolate this test from the main Active Directory server, I will drag in a separate server to represent the target asset.
1. Let's go to the bottom-left device menu, select End Devices, and drag a fresh Server onto the workspace. Name it Database_Staging.
2. Connect a black Copper Straight-Through cable from Database_Staging (FastEthernet0) to Switch Port FastEthernet0/13.
<img width="954" height="1007" alt="image" src="https://github.com/user-attachments/assets/2b769776-4803-45d5-842c-931159f853c1" />

3. Go to your Switch CLI and type these quick commands to wake that port up (since it was blocked in our previous mass-lockdown step):
```
configure terminal
interface fastethernet 0/13
no shutdown
exit
```
<img width="745" height="240" alt="image" src="https://github.com/user-attachments/assets/ed26f799-db92-461b-b6b4-fde057e95496" />

4. Click Fast Forward Time (>>) to turn the link green.
<img width="906" height="1009" alt="image" src="https://github.com/user-attachments/assets/213dbb80-611d-43c0-9516-236f264bf343" />

5. Click on the Database_Staging server -> Go to Desktop -> IP Configuration -> Select DHCP. It will pull a dynamic IP address automatically (192.168.1.57).
<img width="746" height="912" alt="image" src="https://github.com/user-attachments/assets/ba8acec8-5418-44ef-8c10-303659284d18" />

---

### 📥 Step 2: Simulate the Insider Sniffing Tap
In a real network, the rogue admin would install Wireshark directly on the staging server to capture traffic entering and leaving its network interface card. In Packet Tracer, we will use the native Sniffer appliance to model this data harvesting action.
1. Drag a Sniffer appliance onto your workspace. Name it Internal_Harvest_Node.
2. To mirror the database server's traffic over to the sniffer, go to your Switch CLI and run a new SPAN monitoring configuration:
```
configure terminal
interface fastethernet 0/11
no shutdown
exit

! Mirror all database replication data (Port 13) over to the sniffer interface (Port 11)
monitor session 2 source interface fastethernet 0/13
monitor session 2 destination interface fastethernet 0/11
```
---
### 🚨 Step 3: Run the Interception & Exfiltration Test
Let's simulate unencrypted database transactions moving across the network:
1. Click on PC 0, open its Web Browser, and browse directly to your database server's IP address (e.g., 192.168.1.57).
2. Because the traffic uses raw HTTP instead of HTTPS, the data moves over the local wire completely exposed.
3. Click on the Internal_Harvest_Node (Sniffer) -> Go to the GUI tab -> Filter for HTTP.
<img width="739" height="895" alt="image" src="https://github.com/user-attachments/assets/dfb9e5a6-9f9a-4a75-a88a-29883f8c694f" />

4. Click on any captured HTTP packet row and inspect the payload at the bottom. The disgruntled employee can read proprietary configurations, code layouts, or raw system names plain as day inside the ASCII panel on the right side. They can now save this PCAP file and sell it to the competitor.
<img width="1902" height="1020" alt="image" src="https://github.com/user-attachments/assets/c653f231-39ae-4fdd-8900-060be1e64f50" />

```
HTTP Data:
Acceptimage/avif,image/webp,image/apng,image/svg+xml,image/*,*/*;q=0.8
Accept-Language: en-us
Accept: */*Connection:
closeHost: 192.168.1.57

Referer
http://192.168.1.57/

User-Agent
Mozilla/5.0 (Windows NT 6.2; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) QtWebEngine/6.8.7 Chrome/130.0.0.0 Safari/537.36
```
Let's look closely at the information the sniffer just intercepted:
* The Destination Target (Host:): It clearly reads 192.168.1.57. This explicitly proves the user at the PC typed in or targeted your secret Database_Staging server coordinate!
* The Entry Route (Referer:): It shows http://192.168.1.57/, confirming they hit the root webpage directly.
* The Device Signature (User-Agent:): It shows the exact web browser details (Chrome/130.0.0.0 Safari/537.36) used to pull the corporate files.
---

### 🔍 Tracking the Insider's Footsteps
This specific packet is the digital footprint of the attack. Even though Cisco Packet Tracer's light engine compresses the final text layout inside individual windows, this capture log contains exactly what a security investigator looks for.

By analyzing this specific text file, an incident response team can trace exactly:
* **Who sent it:** The machine with the browser signature.
* **What they wanted:** Direct, unencrypted access to your staging asset.
* **How they got it:** Over an open, unencrypted network path.

I have now successfully captured, read, and verified the insider threat's traffic payload! This completely confirms the data harvesting phase of my custom cyber scenario.
---

### Scenario: The Apprehension and System Hardening
This incident response scenario perfectly demonstrates the full lifecycle of security operations: **Detection, Containment, Eradication, and Hardening.**

### 🚨 The Incident Response Scenario: The Insider is Caught

**1. Detection**
The Security Operations Center (SOC) team notices an anomaly on the network monitoring dashboard. A massive volume of data packets is streaming continuously out of Switch Port Fa0/11 (the Sniffer port).
**2. Forensic Analysis**
An incident response engineer logs into the core switch and runs the audit commands you learned earlier:
* Core-Switch-01# show users
* Core-Switch-01# show tcp brief

The switch output reveals that an active administrative session is running from PC 0, using the hijacked credentials of a junior support employee. The team checks the physical security logs and security cameras for that cubicle and identifies the disgruntled senior systems administrator sitting at the keyboard.

**3. Apprehension & Containment**
Before the admin can upload the copied data files to the external competitor, the security team initiates immediate containment protocols:
* The network engineer logs into the switch and instantly shuts down the compromised port:
```
Core-Switch-01(config)# interface fastethernet 0/1
Core-Switch-01(config-if)# shutdown
```
<img width="909" height="1003" alt="image" src="https://github.com/user-attachments/assets/c99564e4-8f4a-4735-bae5-89990a68525d" />

* Corporate security personnel arrive at the desk, terminate the employee's physical access, seize the malicious harvest node, and escort them out of the facility.
```
**NOTE:**
We targeted interface fastethernet 0/1 because that is the exact physical port where PC 0 is plugged into the switch!

Remember our original network wiring checklist from when we started this fresh build:
* PC 0 ➡️ Connected to Switch Port FastEthernet 0/1

In the incident response scenario, the forensic analysis team discovered that the disgruntled senior administrator had physically walked over to PC 0 and hijacked it to launch their sniffing attack.

By typing interface fastethernet 0/1 followed by shutdown, the security engineer sends an immediate software kill-signal to that exact port. This instantly cuts off the electricity to the wire on Port 1, drops PC 0 offline, and freezes the malicious admin's active session in its tracks before they can hit "upload" or save another file.

It is the fastest way to achieve network containment during an active cyber breach!
```
### 🛡️ The Hardening Phase: Activating Global Encryption
Now that the threat is removed, let's fix the structural vulnerability by encrypting all traffic loops.

### Step 1: Force HTTPS (Secure Web Browsing) on the Servers
By activating HTTPS, all website content and text entries are scrambled via SSL/TLS encryption before leaving the server.
1. Click on your Server (and then repeat this for Database_Staging).
2. Go to the Services tab and click on HTTP in the left menu.
3. Toggle the standard HTTP switch to OFF.
<img width="758" height="567" alt="image" src="https://github.com/user-attachments/assets/c6acc8e2-2589-4c3c-8195-502f4610d486" />

4. Ensure the HTTPS switch is set to ON.Now, any device attempting to read corporate data must use the secure path (https://globalbank.com).
<img width="753" height="413" alt="image" src="https://github.com/user-attachments/assets/c02ea4ae-a27b-4552-baf5-178e733293a3" />

### Step 2: Lock Down the Switch Terminal via SSH v2
Let's permanently enforce encrypted remote management to neutralize line sniffing.

Click on your Switch, open the CLI tab, unlock it using LocalAdminPass123 / CorporateAdmin789, and paste this clean encryption sequence:
```
configure terminal
ip domain-name globalbank.com
crypto key generate rsa   ! [Type 1024 if prompted and hit Enter]
ip ssh version 2
line vty 0 4
transport input ssh
exit
exit
write memory
```
<img width="748" height="869" alt="image" src="https://github.com/user-attachments/assets/650b31ef-e914-4ab6-9a97-4aa345996a13" />

---

### 🧪 The Final Security Verification (The Scramble Test)
Let's verify how the encryption defenses look to a hacker or another rogue sniffer:
1. Go to PC 1 (or any active PC), open the Web Browser, and attempt to access the unencrypted site: http://globalbank.com. **The connection will fail instantly**.
<img width="756" height="325" alt="image" src="https://github.com/user-attachments/assets/0a4a0100-4446-4dea-8b74-2f275578d5ae" />

2. Now, enter the secure URL: https://globalbank.com. The portal dashboard loads up perfectly.
<img width="759" height="499" alt="image" src="https://github.com/user-attachments/assets/9b0781c1-1795-4f48-af0d-37bbcc79dbc3" />

3. Open your Internal_Harvest_Node (Sniffer) tool and view the fresh log files.

### 🔍 The Result:
* There will no longer see any plain "HTTP" or "TELNET" protocol rows.
<img width="759" height="908" alt="image" src="https://github.com/user-attachments/assets/a502156f-fb54-4319-939d-8f2510dd76a5" />

* Instead, the sniffer will log rows labeled HTTPS and SSH.
* Click on any of those new packets and scroll to the bottom text window. Instead of readable English text, usernames, or paths, the data payload field is completely filled with a scrambled, chaotic block of mathematical gibberish.

Your data is now 100% secure from internal and external eavesdroppers!
With the threat contained, the engineering team immediately transitions to emergency remediation—transitioning legacy legacy configurations to encrypted standards (HTTPS and SSH) to ensure that any future sniffing attempts yield nothing but unreadable data.
