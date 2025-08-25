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

web-vm - IP: 192.168.56.101
Role: Frontend application server. Initiates connections to the database only. No direct external or internal access to other resources.

db-vm - IP: 192.168.56.102
Role: Backend database server. Only receives traffic on port 3306 (MySQL) from the web server. No outbound traffic or lateral communication.

admin-vm (optional) - IP: 192.168.56.103
Role: Management/jumpbox system. Used for remote administration over SSH. Can reach both web-vm and db-vm via SSH but has no internet access.

Each VM represents a different function or trust zone, and policies are designed to strictly control how and whether communication is allowed between them.

All VMs on same VirtualBox Host-Only Adapter.

## Step by Step

1. Launched VirutalBox & Downloaded Ubuntu Server ISO
2. Created VMs in VirtualBox (x3)

      You’ll be making 3 VMs:
      
         - web-vm
         - db-vm
         - admin-vm (optional but good for practice)
      
      For each VM, do the following:
      
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

4. Install Ubuntu Server on Each VM

🔧 What to Do After Ubuntu Server ISO Finishes Downloading
1. Open VirtualBox

Launch VirtualBox on your Windows system.

2. Create a New VM

Click “New” and fill this out:

Name: web-vm (or db-vm, or admin-vm, depending on which one you’re creating first)

Type: Linux

Version: Ubuntu (64-bit)

Click Next

3. Allocate Resources

RAM: 1024 MB (1 GB minimum, 2048 MB is better)

CPU: 1 or 2 (adjust if your system has more cores)

Click Next

4. Create a Virtual Hard Disk

Select: Create a virtual hard disk now

Disk type: VDI

Storage: Dynamically allocated

Size: 10 GB or more

Click Create

5. Attach the Ubuntu ISO

Once the VM is created:

Select the VM (e.g., web-vm)

Click Settings > Storage

Under Controller: IDE, click the Empty disk icon

On the right side, click the CD icon, then choose “Choose a disk file…”

Browse to where your .iso file was downloaded (e.g., Downloads)
→ Select your ubuntu-22.04.4-live-server-amd64.iso

Click OK

6. Set Network to Host-Only

Go to Settings > Network

Adapter 1: Enabled

Attached to: Host-Only Adapter

(Optional: Add Adapter 2 as NAT if you want internet access later)

7. Start the VM

Click Start

The VM will boot from the ISO file (like a real server booting from a CD)

You’ll be in the Ubuntu Server installer

8. Install Ubuntu Server

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

Configure your static IP address using netplan

SSH'd into VM using cmd bc couldn't paste commands in VM. Now I can paste.









