# Azure-Infrastructure-Process
Enterprise Ready Azure Infrastructure Setup

###Networking:
Hub-and-Spoke VNET Topology:
Created a Hub-VNET with an address space of 10.0.0.0/16 to act as the central connectivity point.
Added two Spoke-VNETs: Spoke1-VNET (10.1.0.0/16) for production and Spoke2-VNET (10.2.0.0/16) for development. This isolates the two environments while allowing them to communicate through the hub.
<br/>
###Subnet Configuration:
Within each VNET, created subnets to segment traffic and resources:
GatewaySubnet: For connecting to on-premises networks or other VNETs (10.0.0.0/24 in Hub-VNET).
AzureFirewallSubnet: Dedicated to hosting the Azure Firewall (10.0.1.0/24 in Hub-VNET).
BastionSubnet: For secure remote access to VMs (10.0.2.0/24 in Hub-VNET).
Management: For hosting management resources (10.0.3.0/24 in Hub-VNET).
AppSubnet: For application servers (10.1.0.0/24 in Spoke1-VNET and 10.2.0.0/24 in Spoke2-VNET).
DataSubnet: For data storage and databases (10.1.1.0/24 in Spoke1-VNET and 10.2.1.0/24 in Spoke2-VNET).
<br/>

###VNET Peering:
Established peering connections between Hub-VNET and each Spoke-VNET. This allows resources in different VNETs to communicate with each other while maintaining network isolation.
<br/>

###Azure Firewall Deployment:
Deployed Azure Firewall in the Hub-VNET's AzureFirewallSubnet. This acts as a centralized security boundary, controlling traffic between the VNETs and the internet.
Configured basic network rules to allow traffic between the spoke networks and to the internet.
Also configured application rules to allow access to specific FQDNs like *.microsoft.com, *.windows.com, and *.windowsupdate.com.
<br/>

###Route Tables:
Created route tables (Spoke1-RT and Spoke2-RT) to force traffic from the Spoke-VNETs to go through the Azure Firewall in the Hub-VNET.
This ensures that all traffic is inspected and filtered by the firewall before reaching the internet or other networks.
<br/>

###Network Security Groups (NSGs):
Created NSGs for each subnet in both Spoke-VNETs.
These NSGs have rules to allow or deny traffic based on source IP, destination IP, and port. This provides an additional layer of security at the subnet level.
<br/>

###Security:
NSG Rules:
You configured detailed rules in the NSGs to control traffic flow within and between subnets.
For example, the Spoke1-Data-NSG only allows traffic from the Spoke1-AppSubnet and management traffic from the Hub-VNET.
Azure Firewall Rules:
You configured network and application rules in the Azure Firewall to control traffic between the VNETs and the internet.
This ensures that only authorized traffic is allowed, protecting your resources from unauthorized access.
<br/>

###Management:
Azure Bastion:
You deployed Azure Bastion in the Hub-VNET.
This provides a secure and seamless way to connect to VMs in your VNETs without needing to expose them to the internet through public IPs.
