# pfSense Firewall - Network Security Lab

![1200px-PfSense_logo](https://github.com/andreiii14/pfsense-firewall-network-security/assets/128039153/e0e06a92-0bfc-4fe4-bd88-16997758b97d)


## Overview
The pfSense Lab Project is a hands-on initiative focused on creating a secure and robust network infrastructure using pfSense, a powerful open-source firewall and router platform. This project is designed to enhance network security, control traffic, and provide secure remote access. The key objectives include the configuration of firewall rules, Network Address Translation (NAT), Virtual Private Network (VPN), traffic shaping, monitoring, intrusion detection/prevention (IDS/IPS), and the implementation of a captive portal.

## TABLE OF CONTENTS
1. [Current Setup](#current-setup)
2. [Firewall Rules](#firewall-rules)
3. [NAT Configuration](#nat-configuration)
4. [VPN Configuration](#vpn-configuration)
5. [Traffic Shaping and QoS](#traffic-shaping-and-qos)
6. [Logging and Monitoring](#logging-and-monitoring)
7. [Intrusion Detection/Prevention (IDS/IPS)](#intrusion-detectionprevention-idsips)
8. [Captive Portal](#captive-portal)
9. [Key Observations and Conclusion](#learnings-and-conclusion)

---
## <a name="current-setup"></a>1. Current Setup

### pfSense VM Configuration

**Configuration:**
- **Network Adapters:**
  - NAT (Network Adapter 1), Host-Only (Network Adapter 2).
- **WAN IP Address:** 192.168.100.4/24.
- **LAN IP Address:** 192.168.1.1.

### Kali Linux VM Configuration

**Configuration:**
- **Network Adapter:** Host-Only.
- **IP Address:** 192.168.1.2.

**Purpose:**
- **NAT Network Adapter:**
  - Network Address Translation (NAT) facilitates internet access for the pfSense VM.
- **Host-Only Network Adapter:**
  - Establishes an isolated internal network for communication between pfSense and Kali Linux VM.

### User1 Ubuntu VM Configuration

**Configuration:**
- **Network Adapter:** NAT.
- **IP Address:** Automatically assigned by pfSense DHCP.

**Purpose:**
- Simulates a user within the NAT network, accessing the internet through pfSense.

### User2 Ubuntu VM Configuration

**Configuration:**
- **Network Adapter:** NAT.
- **IP Address:** Automatically assigned by pfSense DHCP.

**Purpose:**
- Another instance simulating a user within the NAT network, similar to User1, accessing the internet through pfSense.

### Network Diagram

```
+------------------------+          +------------------------+
|                        |          |                        |
|        Internet        |          |                        |
|                        |          |                        |
+-----------|------------+          +-----------|------------+
            |                                   |
            |                                   |
            |                                   |
            |                                   |
            |                                   |
            |                                   |
            |                                   |
+-----------v------------+          +-----------v------------+
|                        |          |                        |
|   WAN: 192.168.100.4   |          |   LAN: 192.168.1.1     |
|                        |          |                        |
|      pfSense VM        |          |   Kali Linux VM        |
|                        |          |   IP: 192.168.1.2      |
|   +--------------+     |          |                        |
|   | NAT Adapter  |     |          |   +----------------+   |
|   | Host-Only    |     |          |   | Host-Only      |   |
|   |              |     |          |   |                |   |
|   +--------------+     |          |   +----------------+   |
|          |             |          |            |           |
|          |             |          |            |           |
|   +------v------+      |          |   +--------v--------+  |
|   | User1        |     |          |   | User2           |  |
|   | IP:          |     |          |   | IP:             |  |
|   | 192.168.1.3  |     |          |   | 192.168.1.4     |  |
|   +-------------+      |          |   +----------------+   |
+------------------------+          +------------------------+
```
---

## <a name="firewall-rules"></a>2. Firewall Rules

### Objective:
Implement robust firewall rules to control inbound and outbound traffic, enhancing network security.

**Steps:**

### Navigate to Firewall Rules

**Steps:**
1. Go to "Firewall" > "Rules."

<img width="900" alt="7 firewall" src="https://github.com/andreiii14/pfsense-firewall-network-security/assets/128039153/db4f9507-34fa-43ae-be4e-25106cf14c30">


### Add Rules

**Steps:**
1. Based on security policies, we added the following rules:

- **Allow HTTPS:**
     - Interface: WAN
     - Protocol: TCP/UDP
     - Source: Any
     - Destination: WAN Address
     - Destination Port Range: 443
     - *Explanation: Enables secure web communication on port 443 for encrypted data transmission and secure browsing.*
<img width="900" alt="8 firewall https" src="https://github.com/andreiii14/pfsense-firewall-network-security/assets/128039153/c8919c79-898d-4a61-8d8c-fa2d3ca758bc">


   - **Block SSH (Port 22):**
     - Interface: WAN
     - Protocol: TCP
     - Source: Any
     - Destination: WAN Address
     - Destination Port Range: 22
     - *Explanation: Prevents unauthorized access attempts via SSH by blocking the corresponding port.*

<img width="900" alt="9 firewall ssh" src="https://github.com/andreiii14/pfsense-firewall-network-security/assets/128039153/643950c1-f1de-4f6e-8b75-0585a753dda2">


   - **Allow OpenVPN:**
     - Interface: WAN
     - Protocol: UDP
     - Source: Any
     - Destination: WAN Address
     - Destination Port Range: Custom OpenVPN Port
     - *Explanation: Permits UDP traffic on a custom OpenVPN port for establishing secure connections.*

<img width="900" alt="10 firewall openvpn" src="https://github.com/andreiii14/pfsense-firewall-network-security/assets/128039153/612c49cb-d718-494f-9e98-036b3e7afbe9">


   - **Block ICMP (Ping):**
     - Interface: WAN
     - Protocol: ICMP
     - Source: Any
     - Destination: WAN Address
     - *Explanation: Restricts ICMP traffic to enhance network security and prevent potential attacks.*

<img width="900" alt="11 firewall icmp" src="https://github.com/andreiii14/pfsense-firewall-network-security/assets/128039153/79b91263-b8c6-49d3-9b90-b03a18545366">


   - **Block Telnet (Port 23):**
     - Interface: WAN
     - Protocol: TCP
     - Source: Any
     - Destination: WAN Address
     - Destination Port Range: 23
     - *Explanation: Prevents unauthorized access attempts via SSH by blocking the corresponding port.*

   - **Allow DNS (Port 53):**
     - Interface: WAN
     - Protocol: UDP/TCP
     - Source: Any
     - Destination: WAN Address
     - Destination Port Range: 53
     - *Explanation: Allows DNS resolution for proper internet functionality.*

   - **Allow NTP (Port 123):**
     - Interface: WAN
     - Protocol: UDP
     - Source: Any
     - Destination: WAN Address
     - Destination Port Range: 123
     - *Explanation: Permits Network Time Protocol for time synchronization.*

   - **Block Unused Ports:**
     - Interface: WAN
     - Protocol: Any
     - Source: Any
     - Destination: WAN Address
     - *Explanation: Blocks access to unused or unnecessary ports, reducing the attack surface.*

### Testing

**Steps:**
1. We verify functionality by accessing allowed services (HTTPS, OpenVPN, DNS and NTP) and checking blockages (SSH, ICMP, Telnet, and unused ports).

**Reasoning:**
- **Control Traffic:**
  - Firewall rules allow precise control over the types of traffic that are permitted or denied, preventing unauthorized access and enhancing overall network security.

- **Security Policies:**
  - Aligning rules with security policies ensures that network behavior complies with established security standards, reducing the risk of potential threats.

By incorporating these rules, we selectively permit essential services while restricting access to potential security vulnerabilities, contributing to a more secure network environment.

---

## <a name="nat-configuration"></a>3. NAT Configuration

### Objective:
Configure Network Address Translation (NAT) to facilitate internet access for devices within the LAN.

### Navigate to Outbound NAT

**Steps:**
1. Go to "Firewall" > "NAT" > "Outbound."

<img width="900" alt="12 NAT" src="https://github.com/andreiii14/pfsense-firewall-network-security/assets/128039153/53c8770c-0c7b-4d3f-a568-1ab3920324c7">


### Set Outbound NAT Mode

**Steps:**
1. Choose "Automatic Outbound NAT Rule Generation."

<img width="900" alt="13 NAT OUTBOUND" src="https://github.com/andreiii14/pfsense-firewall-network-security/assets/128039153/f7f3dc11-06c0-4297-849e-02d8dcdc8ac5">

### Testing

**Steps:**
1. We can confirm via devices on LAN, by being able to access the internet.

### Reasoning

**Reasoning:**
- **Internet Access:**
  - NAT enables devices with private IP addresses to access the internet.

**Benefits:**
- **Internet Connectivity:**
  - NAT ensures devices on the LAN can access the internet.

---

## <a name="vpn-configuration"></a>4. VPN Configuration (OpenVPN)


### Objective:
Configure OpenVPN on the VM to establish a secure connection for remote clients.

### Steps:

#### 1. Navigate to "VPN" > "OpenVPN."

<img width="900" alt="1 openvpn " src="https://github.com/andreiii14/pfsense-firewall-network-security/assets/128039153/f8670141-8221-47d5-9caa-345ccce03f2e">

#### 3. Click on the "Wizards" tab.

#### 4. Configure the server setup

<img width="900" alt="3 openvpn wizard" src="https://github.com/andreiii14/pfsense-firewall-network-security/assets/128039153/3cb44bfa-20ed-4c1c-85da-e2be6b08258a">

#### 6. Configure the following settings:
   - **Interface:** LAN (connected to the local network).
   - **Description:** AndreiOpenVPN (to distinguish the purpose).
   - **VPN Server:** Check to enable the VPN server.


#### 7. Configure Virtual Private Network (VPN) settings:
   - **Tunnel Network:** Set to a different subnet, e.g., 10.8.0.0/24.
   - **Redirect Gateway:** Check to force client-generated IPv4 traffic through the VPN tunnel.
   - **Local Network:** Enter the existing LAN network (e.g., 192.168.1.0/24) to allow VPN clients to access local resources.
   - **Concurrent Connections:** Set a maximum number, e.g., 20 clients.
   - **Inter-Client Communication:** Check to allow communication between clients connected to the same OpenVPN server.
   - **Duplicate Connections:** Choose to allow or deny multiple connections from the same client to prevent simultaneous connections.

<img width="900" alt="4 openvpn tunnel" src="https://github.com/andreiii14/pfsense-firewall-network-security/assets/128039153/4f82307e-fc2c-453b-9e08-d787e2937e02">

#### 9. Configure client settings:
   - **Topology:** Select "Subnet."
   - **DNS Server 1:** Enter 8.8.8.8.

#### 12. Confirm Firewall rule has been set to Pass to allow OpenVpN 
   - This was completed in the previous step, in Firewall Rules - However it can be configured while setting up OpenVPN as per below:

<img width="900" alt="5 openvpn firewall" src="https://github.com/andreiii14/pfsense-firewall-network-security/assets/128039153/294b1bb6-4ac3-46c2-a486-1ad730680fc3">



#### 12. Confirm and click "Finish" to complete the OpenVPN wizard.
<img width="900" alt="6 openvpn finish" src="https://github.com/andreiii14/pfsense-firewall-network-security/assets/128039153/71b3e709-c4bd-47e0-baaa-e43ad0df7f9c">


### Reasoning:
By configuring OpenVPN, we establish a secure and private connection for remote clients, ensuring encrypted communication and controlled access to the local network.

---

## <a name="traffic-shaping-and-qos"></a>5. Traffic Shaping and QoS

### Objective:
Implement Traffic Shaping and Quality of Service (QoS) to optimize bandwidth usage and prioritize critical applications, ensuring efficient network performance. Similarly, enabling VoIP prioritization to enhance communication quality, providing a seamless experience for voice-over-IP applications.

### Navigate to Traffic Shaper Wizards

**Steps:**
1. Go to "Firewall" > "Traffic Shaper" > "Wizards."
<img width="900" alt="16 traffic shaper" src="https://github.com/andreiii14/pfsense-firewall-network-security/assets/128039153/8d54fea4-cc41-4473-aad5-c027b0f08842">

### Select Multi-LAN/WAN
<img width="900" alt="17 - multi lan-wan" src="https://github.com/andreiii14/pfsense-firewall-network-security/assets/128039153/5f9613ee-a2f3-42fa-bbcf-6e383ed0555b">

**Steps:**
1. Choose the appropriate wizard and define bandwidth limits based on internet connection speed. (in this case, I used 10,000 Kbit/s Upload speed and 100,000 Kbit/s Download speed.)
2. Enable VoIP prioritization by checking the "Enable" box.
3. Configure VoIP settings:
   - Provider: Choose "Generic" if not listed.
   - Upstream SIP Server: Enter the SIP server IP if required.
   - Connection WAN #1:
     - Upload Speed: 5 Mbps
     - Download Speed: 50 Mbps
<img width="900" alt="18 - traffic shaper" src="https://github.com/andreiii14/pfsense-firewall-network-security/assets/128039153/74479aa7-0c18-4417-b7f1-b7a7fc1f7875">
<img width="900" alt="19 voip" src="https://github.com/andreiii14/pfsense-firewall-network-security/assets/128039153/c70c0c26-3b31-45cf-903b-ea6143ed9c65">

### Enable Peer-to-Peer Networking

**Steps:**
1. Enable Peer-to-Peer networking.
2. Lower the priority of Peer-to-Peer traffic.
3. Configure P2P Catch-All:
   - Enable if necessary to feed uncategorized traffic to the P2P queue.
4. Set Bandwidth Limit for P2P traffic:
   - Example: Set it to 2 Mbps (2000 Kbps).
<img width="900" alt="20 peer to peer netowrking" src="https://github.com/andreiii14/pfsense-firewall-network-security/assets/128039153/6653f836-af0a-4e0a-a09e-056a7347f92e">

### Testing

**We can test by doing the following:**
1. Monitor bandwidth usage and verify traffic prioritization.
2. Initiate a VoIP call and ensure prioritization by confirming clear and uninterrupted communication.
3. Generate Peer-to-Peer traffic and observe its lower priority compared to other types of traffic.
4. Check system logs for logged packets to validate logging settings.

### Reasoning

**VoIP Prioritization:**
- Enabled to prioritize VoIP traffic, ensuring clear and uninterrupted communication.
- Configured settings to accommodate assumed network speeds, optimizing VoIP performance.

**Peer-to-Peer Networking:**
- Lowering the priority of P2P traffic ensures that critical applications receive higher priority, enhancing overall network performance.
- Bandwidth limit prevents P2P traffic from consuming all available bandwidth, maintaining optimal network conditions.
- Specific protocols can be enabled/disabled based on the network's needs, allowing customization for better traffic control.

**Logging and Monitoring:**
- Enabling detailed logging provides visibility into handled, blocked, and matched packets, aiding in network troubleshooting.
- Monitoring options on both LAN and WAN sides contribute to comprehensive network visibility.

Configuring Traffic Shaping, VoIP prioritization, Peer-to-Peer networking, and Logging/Monitoring enhances network performance, optimizes bandwidth, and provides comprehensive visibility into network activities, contributing to better security and control.

---

## <a name="logging-and-monitoring"></a>6. Logging and Monitoring

### Objective:
Establish an effective logging and monitoring system to analyze network activity, ensuring comprehensive security analysis.

### Access System Logs Settings

**Steps:**
1. Navigate to "Status" > "System Logs" > "Settings."

### Configure Log Settings

**Steps:**
1. Check relevant log options for effective monitoring.

### Graph Explanation
**States:**
This metric shows the number of states tracked by the firewall. It's useful for monitoring the number of active connections and sessions on our network.

**Memory:**
Monitoring memory usage is crucial to ensure that our pfSense system has sufficient resources. This metric shows the amount of RAM in use over time.

**Processor:**
The processor metric displays CPU usage over time. Monitoring CPU usage helps us understand how heavily loaded our system's CPU is.

**mbuf Clusters:**
The "mbuf clusters" metric is related to network memory usage. mbufs (memory buffers) are used to manage network data in memory. Monitoring mbuf clusters helps us understand network memory utilization.

**Left Axis: States**
If we want to monitor overall system resource usage:

**Left Axis: Memory, Processor**
If we want to focus on network memory usage:

**note: We can customize the time range for the graphs to view data over different intervals (e.g., 1 hour, 8 hours, 1 day, etc.).**

<img width="647" alt="22 logging" src="https://github.com/andreiii14/pfsense-firewall-network-security/assets/128039153/f2ea41c0-fca3-4121-9d51-da9402897fa1">

### Reasoning

**Reasoning:**
- **Security Analysis:**
  - Logging and monitoring enable analysis of network activity for security purposes.

**Benefits:**
- **Security Insights:**
  - Regular monitoring helps identify potential security events or issues.

---

## <a name="intrusion-detectionprevention-idsips"></a>7. Intrusion Detection/Prevention (IDS/IPS)

### Objective:
Implement Suricata as an Intrusion Detection/Prevention System (IDS/IPS) on the WAN interface to actively monitor and prevent potential security threats.

### We Chose Suricata for IDS/IPS

**Steps:**
1. Install Suricata
<img width="900" alt="28 suricata success" src="https://github.com/andreiii14/pfsense-firewall-network-security/assets/128039153/6bccc328-56d5-4cba-9b78-a90db5f8c828">

### Configure Suricata IDS/IPS Settings

**Steps:**
1. Adjust settings according to network requirements and security policies. In this case, we configured Suricata to simulate a real world corporate network

### Suricata WAN Settings:

### General Settings:
- **Enable:** Checked
- **Interface:** WAN
- **Description:** Suricata on WAN

**Logging Settings:**
- **Send Alerts to System Log:** Checked
- **Enable Stats Collection:** Checked
- **Enable HTTP Log:** Checked
- **Append HTTP Log:** Checked
- **Log Extended HTTP Info:** Checked
- **Enable TLS Log:** Checked
- **Enable File-Store:** Unchecked (not needed in this lab, as it consumes significant disk space)
- **Enable Packet Log:** Unchecked (not needed in this lab, as it can consume significant disk space)
- **Enable Verbose Logging:** Unchecked

**EVE Output Settings:**
- **EVE JSON Log:** Checked

**Alert and Block Settings:**
- **Block Offenders:** Checked

**Performance and Detection Engine Settings:**
- **Run Mode:** AutoFP
- **AutoFP Scheduler Type:** Hash
- **Max Pending Packets:** 1024
- **Detect-Engine Profile:** Medium
- **Pattern Matcher Algorithm:** Auto
- **Signature Group Header MPM Context:** Auto
- **Inspection Recursion Limit:** Leave blank
- **Delayed Detect:** Unchecked
- **Promiscuous Mode:** Checked
- **Interface PCAP Snaplen:** 1518

**Networks Suricata Should Inspect and Protect:**
- **Home Net:** Default Home Net
- **External Net:** Default External Net

**Alert Suppression and Filtering:**
- **Alert Suppression and Filtering:** None (Default)

<img width="900" alt="29 suricata" src="https://github.com/andreiii14/pfsense-firewall-network-security/assets/128039153/0dbfd4a2-8da1-4fcc-89e5-6c2d33fb15f3">
<img width="900" alt="30 suricata" src="https://github.com/andreiii14/pfsense-firewall-network-security/assets/128039153/35e171ef-9a84-42aa-bcf7-8a88842bd14f">

### Save and Apply Changes:
After configuring these settings, we need to "Apply Changes" for the settings to take effect.

### Reasoning

**Reasoning:**
- **Network Security:**
  - IDS/IPS enhances network security by monitoring and preventing suspicious activities.

**Benefits:**
- **Threat Detection:**
  - IDS/IPS systems actively detect and respond to potential security threats.

---

## <a name="captive-portal"></a>8. Captive Portal

### Objective:
Implement and configure the Captive Portal feature on the designated interface (in our case, the LAN) to enhance control over guest or restricted network access.

### Navigate to Captive Portal Settings

**1. Go to "Services" > "Captive Portal."**
<img width="900" alt="23 captive portal" src="https://github.com/andreiii14/pfsense-firewall-network-security/assets/128039153/b7211f76-95de-4e3c-bb1c-b4c6bb21ddf7">

### Configure Basic Settings

#### Steps:

**1. Enable Captive Portal, provide a description, and select the interface (e.g., LAN).**

**2. Set maximum concurrent connections, idle timeout, hard timeout, etc.**

**3. Enable logout popup window, set pre-authentication and post-authentication redirection URLs.**

**4. Set concurrent user logins, MAC filtering, pass-through settings, etc.**
<img width="531" alt="24 - captive" src="https://github.com/andreiii14/pfsense-firewall-network-security/assets/128039153/db24861e-19a3-4a9c-b428-75d912865693">


### Configure Bandwidth Restriction and Customization

-  Enable per-user bandwidth restriction, use a custom captive portal page, etc.
<img width="900" alt="25 - captive" src="https://github.com/andreiii14/pfsense-firewall-network-security/assets/128039153/26d3ade4-36ba-4d0e-a16b-f4088d107aef">

### Configure HTTPS Options

- Enable HTTPS login, choose authentication method, and configure the authentication server.
<img width="900" alt="27 - captive" src="https://github.com/andreiii14/pfsense-firewall-network-security/assets/128039153/0f7b054e-b5ed-499b-bf97-cd78fdc43ab6">

### Configure Logo and Background

- Display custom logo and background images.
<img width="900" alt="26 - captive" src="https://github.com/andreiii14/pfsense-firewall-network-security/assets/128039153/64545397-95ee-404f-b675-3e12282cd94c">

### Reasoning

**Reasoning:**
- **Guest Access Control:**
  - Captive Portal provides controls and customizes guest or restricted network access.
  
---

## <a name="learnings-and-conclusion"></a>9. Key Observations and Conclusion

### Key Observations:

1. **Firewall Rules Implementation:** The meticulous design and enforcement of firewall rules empower precise control over network traffic, ensuring that security policies are effectively implemented.

2. **NAT Configuration:** The successful setup of Network Address Translation (NAT) allows devices within the LAN to seamlessly access the internet, maintaining both security and connectivity.

3. **VPN Establishment:** The implementation of OpenVPN creates a secure and private connection for remote clients, showcasing the importance of secure remote access to network resources.

4. **Traffic Shaping and QoS:** The application of traffic shaping and Quality of Service (QoS) measures optimizes bandwidth usage, leading to improved network performance and prioritization of critical applications.

5. **Logging and Monitoring:** The configured logging and monitoring system provides a robust mechanism for security analysis, allowing for the identification and response to potential security events.

6. **Intrusion Detection/Prevention (IDS/IPS):** The integration of Suricata enhances network security by actively monitoring and preventing suspicious activities, contributing to a proactive defense strategy.

7. **Captive Portal for Guest Access Control:** The Captive Portal implementation facilitates controlled and customized access for guests or restricted users, adding an additional layer of access control.

8. **Learnings and Skill Enhancement:** This project has significantly contributed to the enhancement of skills in network security and administration, offering practical insights into real-world scenarios.

### Conclusion

This comprehensive pfSense network security project has yielded valuable insights and practical experience in configuring and fortifying network defenses. The implementation of crucial features including firewall rules, NAT, VPN, traffic shaping, monitoring, and Captive Portal establishes a resilient and secure network infrastructure. The culmination of these configurations not only enhances the overall security posture but also contributes to a more efficient and controlled network environment.
