# Installing A Virtual Router for my VMs   
## Simulating Real-World Routing with OPNsense and Virt-Manager.   
   
**Goal: **To simulate enterprise network routing by configuring OPNsense as a virtual router that provides WAN connectivity to isolated VMs.   
   
### **Environment:**   
| **System Component** |                  **Specification / Role** |
|:---------------------|:------------------------------------------|
|              Host OS |                             Fedora 42 KDE |
|       Virtualization |                   Virt-Manager (KVM/QEMU) |
|            Router VM |              OPNsense (Firewall & Router) |
|              Clients |                            2 × Kali Linux |
|       Network Design | NAT (WAN) + Routed LAN (192.168.100.0/24) |

### **Network Configuration:**   
|   Device |                                 IP Address |   Subnet Mask |           Role | Network Type |
|:---------|:-------------------------------------------|:--------------|:---------------|:-------------|
| OPNsense | routed - 192.168.100.2 wan - 192.168.1.242 | 255.255.255.0 | Gateway/Router | NAT / Routed |
|   Kali 1 |                             192.168.100.10 | 255.255.255.0 |         Client |       Routed |
|   Kali 2 |                             192.168.100.20 | 255.255.255.0 |         Client |       Routed |

**Topology:**   
![image](files/image.png)    
   
### **Configuration Highlights:**   
- Deployed OPNsense with two virtual NICs: one in NAT mode for WAN and one in an isolated mode for the LAN network.   
- Assigned static LAN IPs for OPNsense (192.168.100.2) and static addresses and DNS for the client VMs.   
- Enabled NAT on the WAN interface to provide connectivity for clients.   
- Configured DNS via OPNsense and validated outbound connectivity from client machines.   
   
   
### **Challenges & Resolutions:**   
**No WAN interface present** - Initially only one virtual NIC. Added a second NIC in NAT mode via Virt-Manager to serve as WAN. 

**LAN clients receive IP but no DNS** - DHCP assignment succeeded but DNS resolution failed. Discovered the DNS Resolver service on OPNsense was disabled; enabled it and set correct DNS servers.

**Clients could ping IPs but not hostnames** - After verifying that clients could reach external IP addresses but not resolve hostnames, I confirmed the issue was isolated to DNS. So I enabled DNS Forwarding, and disabled OPNsense forced DNS to allow internal clients to relay DNS queries through the router to upstream resolvers (1.1.1.1 / 8.8.8.8).   
![image](files/image_t.png)    
^Validated DNS resolution and external connectivity by resolving `www.youtube.com` and loading web pages successfully.   
   
### **Outcomes & Lessons Learnt:**   
I successfully built a virtual router environment where internal client VMs gained internet access via OPNsense’s NAT and routing configuration. This setup functions similarly to a production network gateway, enabling me to toggle connectivity between isolated lab scenarios and external access.
   
This lab reinforced the importance of selecting compatible virtual hardware for OSs, verifying service dependencies (such as DNS) in a routing environment, and systematically diagnosing connectivity layers (physical/virtual NIC → routing → DNS).   
**Next steps:** Introduce VLAN segmentation, firewall rule sets, intrusion detection (IDS) monitoring, and stronger authentication mechanisms to simulate enterprise‐grade network defense.   
   
### **Key Takeaways:**   
- Learned how OPNsense handles interface assignment and NAT translation.   
- Gained understanding of how isolated LANs receive internet access through routing.   
- Discovered how DNS and NAT interact when virtual networks are misconfigured.   
   
   
