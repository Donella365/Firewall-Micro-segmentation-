# Firewall-Based Microsegmentation 

In this project, I'm implementing micro-segmentation using firewalls. I am utilizing micro segmentation as a part of my implementation of zero trust. 

## Tools Used:

VirtualBox (for virtualization)

Ubuntu Server (VM OS)

iptables (built-in firewall utility in Linux)

draw.io ( for network diagrams)

## Network Architecture

### Diagram 1: Traditional Architecture

One perimeter firewall

Internal traffic flows freely

<img width="640" height="579" alt="fw2" src="https://github.com/user-attachments/assets/66f8a09a-3470-48be-a3ff-a4dced811e10" />

### Diagram 2: Microsegmented Architecture

Firewalls placed logically at the access/switch layer (host-based firewalls)

Only explicitly allowed traffic can flow

No implicit trust between workloads

<img width="566" height="567" alt="fw1" src="https://github.com/user-attachments/assets/478a554c-c6ee-4dda-b028-fbfbc647b374" />

## Zero Trust Enforcement

This lab enforces Zero Trust based on NIST SP 800-207 guidance.

- All communication is blocked by default (no implicit trust)
- Firewall rules enforce explicit per-resource permissions (e.g., only MySQL from web to db)
- Workloads (VMs) have isolated trust boundaries
- Outbound access is denied unless explicitly allowed — stopping exfiltration early
- Communication is limited regardless of subnet, IP range, or internal/external location

## Setup
### VMs and Their Roles:

web-vm - 
Role: Frontend application server. Initiates connections to the database only. No direct external or internal access to other resources.

db-vm - 
Role: Backend database server. Only receives traffic on port 3306 (MySQL) from the web server. No outbound traffic or lateral communication.

Each VM represents a different function or trust zone, and policies are designed to strictly control how and whether communication is allowed between them.

All VMs on same VirtualBox Host-Only Adapter.

## Setup Steps

### 1. Launched VirutalBox & Downloaded Ubuntu Server ISO

---

### 2. Created First VM in VirtualBox

Created `web-vm`. Later cloned it to create `db-vm`.

**Settings:**
- RAM: 1–2 GB
- Disk: 10 GB (dynamically allocated)
- CPUs: 1–2 cores
- Network: Host-Only Adapter (Adapter 1), optional NAT (Adapter 2)
- Attached ISO via:  
  `Settings > Storage > Empty Optical Drive > Choose ISO`

  ---
  
### 3. Installed Ubuntu Server on VM

Inside VM:
- Selected language: English
- Set hostname (e.g., `web-vm`)
- Created user account
- Chose: **Install OpenSSH server**
- Used guided partitioning
- Rebooted and was logged into a fully working Ubuntu Server VM. 

---
### 4. Assigned Static IP Using Netplan

Edited `/etc/netplan/00-installer-config.yaml`:

		```yaml
		network:
		  version: 2
		  ethernets:
		    enp0s3:
		      dhcp4: no
		      addresses: []
		      gateway4: []
		      nameservers:
		        addresses: []

Applied config:

		sudo chmod 600 /etc/netplan/00-installer-config.yaml
		sudo netplan apply
  ---
  
### 5. Created db-vm (Next VM)
	• Cloned web-vm in VirtualBox to save time 
	• Changed hostname to db-vm 
		○ sudo hostnamectl set-hostname db-vm 
	• Assigned static IP
	• Logged in and tested connectivity from web-vm - Tested basic ping, SSH access, and connectivity between web-vm and db-vm.

---

### 6. Applied iptables Firewall Rules
	• On web-vm: restricted outbound traffic to only allow port 3306 to db-vm
	• On db-vm: restricted inbound traffic to only accept 3306 from web-vm
	• Blocked everything else by default (default-deny model)
 
		 Default Policy: Deny All
		sudo iptables -P INPUT DROP
		sudo iptables -P OUTPUT DROP
		sudo iptables -P FORWARD DROP
		
		On db-vm (MySQL server):
		
		Allow inbound MySQL from web-vm:
		
		sudo iptables -A INPUT -p tcp --dport 3306 -s 192.168.56.101 -j ACCEPT
		
		
		Allow return traffic:
		
		sudo iptables -A OUTPUT -p tcp --sport 3306 -d 192.168.56.101 -j ACCEPT
		sudo iptables -A INPUT -m conntrack --ctstate ESTABLISHED,RELATED -j ACCEPT
		sudo iptables -A OUTPUT -m conntrack --ctstate ESTABLISHED,RELATED -j ACCEPT
		
		On web-vm (frontend app):
		
		Allow outbound MySQL to db-vm:
		
		sudo iptables -A OUTPUT -p tcp --dport 3306 -d 192.168.56.102 -j ACCEPT
		
		
		Allow return traffic:
		
		sudo iptables -A INPUT -p tcp --sport 3306 -s 192.168.56.102 -j ACCEPT
		sudo iptables -A INPUT -m conntrack --ctstate ESTABLISHED,RELATED -j ACCEPT
		sudo iptables -A OUTPUT -m conntrack --ctstate ESTABLISHED,RELATED -j ACCEPT
		
		🔐 All other traffic is implicitly dropped.

  ---

  ### 7: Tested Firewall Enforcement

• Tested allowed and denied connections using:
		○ ping
		○ nc -zv
		○ curl
	• Verify segmentation works
	• Optionally enable logging for dropped packets

---

### 8: Persisted iptables Rules

Installed iptables-persistent:

sudo apt install iptables-persistent
sudo netfilter-persistent save
10. Persist Firewall Rules
Use iptables-persistent or a script to keep your rules after reboot


Documented Traffic Flow & Security Justification
	• Labeled each VM by function (frontend, database, mgmt)
	• Show which flows are allowed and why (Zero Trust justification)

---

### Outcome

Successfully implemented host-based microsegmentation using iptables

Confirmed Zero Trust posture with enforced isolation and minimal access

Each VM can only do what it's explicitly allowed to—nothing more







