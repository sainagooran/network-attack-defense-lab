# Network Attack & Defense Lab

A small network security lab built with Cisco Packet Tracer.

This project demonstrates how network segmentation and ACLs can be us# Network Attack & Defense Lab

A small network security lab built with Cisco Packet Tracer.

This project was created as a hands-on networking and cybersecurity project. The main goal was to build a small organizational network, separate different types of devices using VLANs, test network access, and use an Access Control List (ACL) to block unauthorized traffic.

## Project Overview

The network represents a small organization with four different network segments:

- Employees
- Servers
- Administration
- Untrusted devices

The project was built in Cisco Packet Tracer using a Cisco 2911 router and a Cisco 2960 switch.

I first configured the network and tested the connectivity between the different VLANs. At this stage, the Unauthorized-PC was able to reach the Web-Server.

After confirming that the basic network connectivity was working, I added an extended ACL to restrict traffic coming from the untrusted network.

The main idea was to keep normal communication working while preventing an unauthorized device from accessing sensitive parts of the network.

## Network Topology

The network has the following logical structure:

```text
                         Router
                       Cisco 2911
                           |
                         Trunk
                           |
                     Main-Switch
                           |
       ------------------------------------------------
       |              |              |               |
    VLAN 10         VLAN 20        VLAN 30         VLAN 40
   Employees        Servers         Admin          Untrusted
       |              |              |               |
   Employee-1     Web-Server      Admin-PC      Unauthorized-PC
   Employee-2     DNS-Server
   Employee-3ed to control access between different parts of a network.

   The router uses router-on-a-stick configuration to provide communication between the VLANs.

The switch separates the devices using VLANs, while the router handles communication between different networks.

The topology image is available here:

topology/network-topology.png

VLAN Design

The network is divided into four VLANs:

VLAN	Name	Network	Purpose
10	EMPLOYEES	192.168.10.0/24	Employee devices
20	SERVERS	192.168.20.0/24	Web and DNS servers
30	ADMIN	192.168.30.0/24	Administrator device
40	UNTRUSTED	192.168.40.0/24	Untrusted device

Using separate VLANs makes it possible to logically separate different types of devices and apply different access rules between them.

Devices

The lab contains:

1 Cisco 2911 Router
1 Cisco 2960 Switch
3 Employee PCs
1 Admin PC
1 Unauthorized PC
1 Web Server
1 DNS Server
IP Addressing
VLAN 10 - Employees
Device	IP Address	Default Gateway
Employee-1	192.168.10.10	192.168.10.1
Employee-2	192.168.10.11	192.168.10.1
Employee-3	192.168.10.12	192.168.10.1
VLAN 20 - Servers
Device	IP Address	Default Gateway
Web-Server	192.168.20.10	192.168.20.1
DNS-Server	192.168.20.11	192.168.20.1
VLAN 30 - Administration
Device	IP Address	Default Gateway
Admin-PC	192.168.30.10	192.168.30.1
VLAN 40 - Untrusted
Device	IP Address	Default Gateway
Unauthorized-PC	192.168.40.10	192.168.40.1
Router-on-a-Stick Configuration

The connection between the router and the switch is configured as a trunk.

The router uses subinterfaces for the four VLANs:

interface GigabitEthernet0/0.10
 encapsulation dot1Q 10
 ip address 192.168.10.1 255.255.255.0

interface GigabitEthernet0/0.20
 encapsulation dot1Q 20
 ip address 192.168.20.1 255.255.255.0

interface GigabitEthernet0/0.30
 encapsulation dot1Q 30
 ip address 192.168.30.1 255.255.255.0

interface GigabitEthernet0/0.40
 encapsulation dot1Q 40
 ip address 192.168.40.1 255.255.255.0

This configuration allows the router to route traffic between the different VLANs.

Switch Configuration

The switch uses access ports for the end devices and a trunk port for the router.

The main port assignments are:

Fa0/1  - Trunk - Router

Fa0/2  - VLAN 10 - Employee-1
Fa0/3  - VLAN 10 - Employee-2
Fa0/4  - VLAN 10 - Employee-3

Fa0/5  - VLAN 30 - Admin-PC

Fa0/6  - VLAN 40 - Unauthorized-PC

Fa0/7  - VLAN 20 - Web-Server
Fa0/8  - VLAN 20 - DNS-Server

The complete switch configuration is available in:

configs/switch-config.txt

The router configuration is available in:

configs/router-config.txt

Security Scenario

The main security scenario is an unauthorized device trying to access internal resources.

The Unauthorized-PC belongs to VLAN 40 and represents a device that should not have access to sensitive internal networks.

Before applying any ACL, I tested the connection from the Unauthorized-PC to the Web-Server.

Unauthorized-PC
192.168.40.10
       |
       |
       v
Web-Server
192.168.20.10

The ping was successful.

This showed that the basic routing between the VLANs was working, but there was no access restriction preventing the untrusted device from reaching the server network.

At this point, I added an extended ACL to control the traffic.

Access Control List

The ACL was designed to block traffic from the untrusted network to the server and admin networks.

access-list 140 deny ip 192.168.40.0 0.0.0.255 192.168.20.0 0.0.0.255
access-list 140 deny ip 192.168.40.0 0.0.0.255 192.168.30.0 0.0.0.255
access-list 140 permit ip any any

The ACL was applied inbound on the VLAN 40 subinterface:

interface GigabitEthernet0/0.40
 ip access-group 140 in

The first rule blocks traffic from VLAN 40 to the server network.

The second rule blocks traffic from VLAN 40 to the admin network.

The final rule allows other traffic that is not specifically blocked.

Connectivity Testing

I tested the network before and after applying the ACL.

1. Unauthorized-PC → Web-Server

Source:

192.168.40.10

Destination:

192.168.20.10

Before ACL

Result: Successful

The Unauthorized-PC was able to reach the Web-Server.

This confirmed that the untrusted network could access the server network before the access control rule was applied.

After ACL

Result: Blocked

Packet loss: 100%

The Unauthorized-PC received:

Reply from 192.168.40.1: Destination host unreachable.

This confirmed that the ACL was preventing access from VLAN 40 to the server network.

2. Unauthorized-PC → Admin-PC

Source:

192.168.40.10

Destination:

192.168.30.10

Result: Blocked

Packet loss: 100%

This confirmed that the second ACL rule was also working and the Unauthorized-PC could not reach the admin network.

3. Employee-1 → Web-Server

Source:

192.168.10.10

Destination:

192.168.20.10

Result: Successful

Packet loss: 0%

This was an important test because the ACL should not prevent normal employee access to the server.

The Employee-1 device was still able to reach the Web-Server after the ACL was applied.

4. Admin-PC → Web-Server

Source:

192.168.30.10

Destination:

192.168.20.10

Result: Successful

This confirmed that the admin network could still communicate with the server network.

Test Results
Source	Destination	Result
Employee-1	Web-Server	Allowed
Admin-PC	Web-Server	Allowed
Unauthorized-PC	Web-Server	Blocked
Unauthorized-PC	Admin-PC	Blocked

The tests confirmed that the ACL was working as intended.

Before and After
Before Applying the ACL

The Unauthorized-PC could access the Web-Server:

Unauthorized-PC
192.168.40.10
       |
       | Allowed
       v
Web-Server
192.168.20.10
After Applying the ACL

The same traffic was blocked:

Unauthorized-PC
192.168.40.10
       |
       X
       |
Web-Server
192.168.20.10

At the same time, legitimate employee access was still allowed:

Employee-1
192.168.10.10
       |
       | Allowed
       v
Web-Server
192.168.20.10
Project Structure
network-attack-defense-lab/
│
├── README.md
│
├── topology/
│   └── network-topology.png
│
├── configs/
│   ├── router-config.txt
│   └── switch-config.txt
│
├── tests/
│   └── connectivity-tests.md
│
└── packet-tracer/
    └── network-attack-defense-lab.pkt
Tools Used
Cisco Packet Tracer
Cisco IOS CLI
VLANs
Router-on-a-Stick
Extended ACLs
Git
GitHub
Markdown
What I Learned

This project helped me understand how VLANs, inter-VLAN routing, and ACLs work together in a small network.

One of the useful parts of the project was testing the network before applying the ACL. The network was fully connected, but that did not mean that every device should be allowed to communicate with every other device.

The ACL provided a simple way to control traffic based on the source and destination networks.

I also practiced basic Cisco IOS configuration, including router subinterfaces, trunk ports, VLAN assignments, and access control rules.

Another thing I learned from the project was the importance of testing both blocked and allowed traffic. It is not enough to confirm that unauthorized traffic is blocked; legitimate traffic should also continue to work after a security rule is introduced.

Limitations

This project is a simulated lab created in Cisco Packet Tracer, so it does not represent a complete production network.

The unauthorized access scenario is also simulated using connectivity tests such as ping. It is not a real penetration test or exploitation of a vulnerable system.

The project mainly focuses on network segmentation, inter-VLAN routing, and basic traffic control using ACLs.

Future Improvements

Some possible improvements for a future version of this lab include:

Adding DHCP for automatic IP configuration
Adding a dedicated management VLAN
Adding more network services
Adding more detailed traffic monitoring
Testing additional ACL rules
Adding logging for denied traffic
Using multiple switches
Building a larger organizational network
Conclusion

This project demonstrates a simple approach to improving security in a small organizational network.

The network was first divided into separate VLANs and configured with inter-VLAN routing. After testing the initial connectivity, an extended ACL was added to restrict traffic from the untrusted network.

The final tests showed that unauthorized access to the server and admin networks was blocked, while legitimate employee and admin access was still working.

The main idea I took from this project is that network security is not only about detecting attacks. Good network segmentation and access control can also reduce the amount of access an unauthorized device has in the first place.

Author

Computer Engineering Student

This project was created as a hands-on networking and cybersecurity project for my technical portfolio.