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

### Zero Trust Enforcement

This lab enforces Zero Trust based on NIST SP 800-207 guidance.

- All communication is blocked by default (no implicit trust)
- Firewall rules enforce explicit per-resource permissions (e.g., only MySQL from web to db)
- Workloads (VMs) have isolated trust boundaries
- Outbound access is denied unless explicitly allowed — stopping exfiltration early
- Communication is limited regardless of subnet, IP range, or internal/external location

### Setup
#### VMs and Their Roles:

web-vm - IP: 192.168.56.101
Role: Frontend application server. Initiates connections to the database only. No direct external or internal access to other resources.

db-vm - IP: 192.168.56.102
Role: Backend database server. Only receives traffic on port 3306 (MySQL) from the web server. No outbound traffic or lateral communication.

admin-vm (optional) - IP: 192.168.56.103
Role: Management/jumpbox system. Used for remote administration over SSH. Can reach both web-vm and db-vm via SSH but has no internet access.

Each VM represents a different function or trust zone, and policies are designed to strictly control how and whether communication is allowed between them.

All VMs on same VirtualBox Host-Only Adapter.

### Step by Step



