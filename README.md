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

## Step by Step

1. Launched VirutalBox & Downloaded Ubuntu Server ISO
2. Created VMs in VirtualBox (x2)

      Made 2 VMs:
      
         - web-vm
         - db-vm
      
      Set the following for the first VM, (cloned this for the 2nd VM)
      
      a. Create New VM
           Name: web-vm (or whatever function)
           Type: Linux
           Version: Ubuntu (64-bit)

     b. Allocate Resources
          
          RAM: 1 GB minimum (2 GB if you can)
          
          CPUs: 1 or 2 is fine
          
          Disk: ~10 GB VDI dynamically allocated
          
      c. Attach the ISO
      
          Go to Settings > Storage
          
          Click the empty optical drive
          
          Load the Ubuntu Server .iso you downloaded
      
      d. Network Configuration
      
          Go to Settings > Network
          
          Adapter 1 → Enable it
          
          Attached to: Host-only Adapter
          
          This isolates the lab from your main network
          
          (Optional) Adapter 2 → NAT
          
          If you want outbound internet for package installs

4. Installed Ubuntu Server on VM


5. Started the VM

      Click Start
      
      In the Ubuntu Server installer:
      
      Select language: English
      
      Choose “Install Ubuntu Server”
      
      Accept network defaults for now
      
      Set a hostname (web-vm, etc.)
      
      Create a user (labuser or whatever you want)
      
      Choose “Install OpenSSH server” when prompted
      
      Continue with installation (guided disk partitioning is fine)
      
      Finish and reboot
      
      After reboot, you’ll be logged into a fully working Ubuntu Server VM.    

Configured static IP address using netplan

6. Created db-vm (Next VM)
	• Cloned web-vm in VirtualBox to save time 
	• Changed hostname to db-vm 
		○ COMMAND - sudo hostnamectl set-hostname db-vm 
	• Assigned static IP
	• Logged in and tested connectivity from web-vm

7. Applied iptables Firewall Rules
	• On web-vm: restricted outbound traffic to only allow port 3306 to db-vm
	• On db-vm: restricted inbound traffic to only accept 3306 from web-vm
	• Blocked everything else by default (default-deny model)

 8. Test Enforcement
	• Tested allowed and denied connections using:
		○ ping
		○ nc -zv
		○ curl
	• Verify segmentation works
	• Optionally enable logging for dropped packets

9. Persist Firewall Rules
Use iptables-persistent or a script to keep your rules after reboot

10. Documented Traffic Flow & Security Justification
	• Labeled each VM by function (frontend, database, mgmt)
	• Show which flows are allowed and why (Zero Trust justification)











