# Enterprise Ready Azure Infrastructure Setup

### 1. Networking: Hub-and-Spoke VNET Topology
Created a Hub-VNET with an address space of 10.0.0.0/16 to act as the central connectivity point. Added two Spoke-VNETs:
- Spoke1-VNET (10.1.0.0/16) for production
- Spoke2-VNET (10.2.0.0/16) for development

This isolates the two environments while allowing them to communicate through the hub.
![image](https://github.com/user-attachments/assets/26eade83-27ae-401d-be4d-7f5e37d73916)


### 2. Subnet Configuration
Within each VNET, created subnets to segment traffic and resources:

In Hub-VNET:
- GatewaySubnet: For connecting to on-premises networks or other VNETs (10.0.0.0/24)
- AzureFirewallSubnet: Dedicated to hosting the Azure Firewall (10.0.1.0/24)
- BastionSubnet: For secure remote access to VMs (10.0.2.0/24)
- Management: For hosting management resources (10.0.3.0/24)

In Spoke-VNETs:
- AppSubnet: For application servers (10.1.0.0/24 in Spoke1-VNET and 10.2.0.0/24 in Spoke2-VNET)
- DataSubnet: For data storage and databases (10.1.1.0/24 in Spoke1-VNET and 10.2.1.0/24 in Spoke2-VNET)

### 3. VNET Peering
Established peering connections between Hub-VNET and each Spoke-VNET. This allows resources in different VNETs to communicate with each other while maintaining network isolation.

### 4. Azure Firewall Deployment
Deployed Azure Firewall in the Hub-VNET's AzureFirewallSubnet. This acts as a centralized security boundary, controlling traffic between the VNETs and the internet. Configured:
- Basic network rules to allow traffic between the spoke networks and to the internet
- Application rules to allow access to specific FQDNs like *.microsoft.com, *.windows.com, and *.windowsupdate.com

### 5. Route Tables
Created route tables (Spoke1-RT and Spoke2-RT) to force traffic from the Spoke-VNETs to go through the Azure Firewall in the Hub-VNET. This ensures that all traffic is inspected and filtered by the firewall before reaching the internet or other networks.

### 6. Network Security Groups (NSGs)
Created NSGs for each subnet in both Spoke-VNETs. These NSGs have rules to allow or deny traffic based on source IP, destination IP, and port. This provides an additional layer of security at the subnet level.

### 7. Security

NSG Rules
Configured detailed rules in the NSGs to control traffic flow within and between subnets. For example, the Spoke1-Data-NSG only allows traffic from the Spoke1-AppSubnet and management traffic from the Hub-VNET.
Azure Firewall Rules
Configured network and application rules in the Azure Firewall to control traffic between the VNETs and the internet. This ensures that only authorized traffic is allowed, protecting your resources from unauthorized access.

### 8. Management
Deployed Azure Bastion in the Hub-VNET. This provides a secure and seamless way to connect to VMs in your VNETs without needing to expose them to the internet through public IPs.


![image](https://github.com/user-attachments/assets/834500a8-6bdb-4497-8b85-97de4b2cbebe)

