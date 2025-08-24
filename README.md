# Firewall-Based Microsegmentation 

In this project, I'm implementing micro-segmentation using firewalls. I am utilizing micro segmentation as a part of my implementation of zero trust. 



## Tools Used:

VirtualBox (for virtualization)

Ubuntu Server (as the OS for both VMs)

iptables (built-in firewall utility in Linux)

draw.io ( for network diagramming)

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
