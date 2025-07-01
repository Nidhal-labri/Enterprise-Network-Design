
# Enterprise Network Design - VLAN, HSRP, OSPF, DHCP, NAT & VoIP

This project represents a Cisco Packet Tracer topology simulating an enterprise-level network infrastructure. It demonstrates:
- VLAN segmentation
- Spanning Tree Protocol (STP)
- HSRP for gateway redundancy
- OSPF for dynamic routing
- DHCP & NAT for automation and internet access
- VoIP and Telephony setup

The network design follows a core-distribution-access model with proper IP addressing, routing, and high availability. Below are the configuration blocks categorized by device.

## 📌 Network Topology

![image_2025-07-01_16-26-45](https://github.com/user-attachments/assets/e82d62de-43b8-419c-bab8-13f6a0e104b0)


---

## 🔁 VLAN Creation on Access & Distribution Switches

**Devices: A-SW1, A-SW2, D-SW1, D-SW2**  
This section creates VLANs for different purposes: Guest, Employee, VOIP, and DATA.
```bash
enable
conf t
vlan 10 
 name Guest
vlan 20
 name Employee
vlan 30
 name VOIP
vlan 40
 name DATA
end
```

---

## 🌐 Spanning Tree Root Configuration

**Primary Root: D-SW1, Secondary Root: D-SW2**  
STP configuration to ensure loop prevention and proper root bridge election.
```bash
# On D-SW1
enable
conf t
spanning-tree vlan 1 root primary
spanning-tree vlan 10 root primary
spanning-tree vlan 20 root primary
spanning-tree vlan 30 root primary
spanning-tree vlan 40 root primary
end

# On D-SW2
enable
conf t
spanning-tree vlan 1 root secondary
spanning-tree vlan 10 root secondary
spanning-tree vlan 20 root secondary
spanning-tree vlan 30 root secondary
spanning-tree vlan 40 root secondary
end
```

---

## 🔁 Trunk Configuration Between Switches

**Devices: D-SW1, D-SW2, A-SW1, A-SW2**  
Trunk ports allow VLAN traffic between switches.
```bash
# On D-SW1 and D-SW2
enable
conf t
interface range gig1/0/1 - 2
switchport mode trunk
end

# On A-SW1 and A-SW2
enable
conf t
interface range gig0/1 - 2, fa0/2
switchport mode trunk
interface fa0/1
switchport access vlan 40
switchport voice vlan 30
end
```

---

## 🌐 VLAN Interface IP Configuration

**Device: D-SW1**  
Assigning IP addresses to VLAN interfaces for Layer 3 routing.
```bash
enable
conf t
interface vlan 1
ip add 192.168.1.1 255.255.255.0
no shutdown
interface vlan 10
ip add 192.168.10.1 255.255.255.0
interface vlan 20
ip add 192.168.20.1 255.255.255.0
interface vlan 30
ip add 192.168.30.1 255.255.255.0
interface vlan 40
ip add 192.168.40.1 255.255.255.0
end
```

---

## 🔄 HSRP Configuration

**Device: D-SW2**  
Configuration for HSRP to provide redundancy in gateway addressing.
```bash
enable
conf t
interface vlan 1
ip add 192.168.1.2 255.255.255.0
no shutdown
standby 1 ip 192.168.1.3
standby 1 priority 101
standby 1 preempt
...
interface vlan 40
ip add 192.168.40.2 255.255.255.0
standby 40 ip 192.168.40.3
standby 40 priority 101
standby 40 preempt
end
```

---

## 🧱 L3 Interfaces Between Switches

**Devices: D-SW1, D-SW2, C-SW1, C-SW2**  
Assigning IPs to routed interfaces for inter-switch communication.
```bash
# D-SW1 and D-SW2
enable
conf t
interface range gig1/0/3 - 4
no switchport
end

# C-SW1
enable
conf t
interface range gig1/0/2 - 5
no switchport
end

# C-SW2
enable
conf t
interface range gig1/0/2 - 4, gig1/0/6
no switchport
end
```

---

## 🧭 OSPF Configuration

**Devices: D-SW1, D-SW2, C-SW1, C-SW2, R1**  
Dynamic routing with OSPF for internal communication.
```bash
# On D-SW1
enable
conf t
ip routing
router ospf 1
router-id 1.1.1.1
network 192.168.1.0 0.0.0.255 area 0
...
end

# On R1
enable
conf t
router ospf 1
router-id 5.5.5.5
network 192.168.100.0 0.0.0.255 area 0
default-information originate
end
```

---

## 🌍 NAT Configuration

**Device: R1**  
Enables NAT for internet access.
```bash
enable
conf t
access-list 1 permit any
ip nat inside source list 1 interface Serial 0/0/0 overload
interface fa0/0
ip nat inside
interface serial 0/0/0
ip nat outside
end
```

---

## ☎️ VoIP Configuration

**Device: R1**  
Basic IP telephony setup for two phones.
```bash
enable
conf t
telephony-service 
max-ephones 2
max-dn 2
ip source-address 192.168.100.1 port 2000
auto assign 1 to 2
ephone-dn 1
number 1001
ephone-dn 2
number 1002
end
```

---

## 🧪 DHCP Configuration

**Device: R1**  
Provides IP addresses to clients across VLANs.
```bash
enable
conf t
ip dhcp excluded-address 192.168.X.1 192.168.X.10
ip dhcp pool DATA
network 192.168.40.0 255.255.255.0
default-router 192.168.40.3
dns-server 8.8.8.8
...
end
```

---

## 💡 DHCP Relay Configuration

**Devices: D-SW1, D-SW2**  
IP helper for forwarding DHCP requests to R1.
```bash
enable
conf t
interface vlan 1
ip helper-address 192.168.100.1
interface vlan 10
ip helper-address 192.168.100.1
...
end
```

---

## 📌 Network Topology

![Network Topology](![image_2025-07-01_16-01-24](https://github.com/user-attachments/assets/fa594f78-1857-482c-bd80-3dc001609a09))

---

## 👨‍💻 Author

Nidhal Labri – [LinkedIn](https://www.linkedin.com/in/nidhal-labri/)
