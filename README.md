# Enterprise Ready Azure Infrastructure Setup

The system now deactivated (I didn't want to spend forever blanking out all the subscription IDs, and public DNS info)
Costs 60 bucks to run over the weekend.

<img src="https://github.com/user-attachments/assets/834500a8-6bdb-4497-8b85-97de4b2cbebe" width="400">

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

![image](https://github.com/user-attachments/assets/088789ba-413e-49c1-8470-20b96733fd30)

In Spoke-VNETs:
- AppSubnet: For application servers (10.1.0.0/24 in Spoke1-VNET and 10.2.0.0/24 in Spoke2-VNET)
- DataSubnet: For data storage and databases (10.1.1.0/24 in Spoke1-VNET and 10.2.1.0/24 in Spoke2-VNET)

![image](https://github.com/user-attachments/assets/72cf0fb5-833c-4bbf-a216-5fe499d74060)

![image](https://github.com/user-attachments/assets/9b5a0cf9-4987-4428-b85e-440eaa262361)

### 3. VNET Peering

Established peering connections between Hub-VNET and each Spoke-VNET. This allows resources in different VNETs to communicate with each other while maintaining network isolation.

![image](https://github.com/user-attachments/assets/7bbb3785-b43e-4f04-aeba-666e6c0d2da8)

### 4. Azure Firewall Deployment

Deployed Azure Firewall in the Hub-VNET's AzureFirewallSubnet. This acts as a centralized security boundary, controlling traffic between the VNETs and the internet. Configured:

- Basic network rules to allow traffic between the spoke networks and to the internet
- Application rules to allow access to specific FQDNs like *.microsoft.com, *.windows.com, and *.windowsupdate.com

![image](https://github.com/user-attachments/assets/614ce55e-42cf-4df8-979b-9047402dc56a)

![image](https://github.com/user-attachments/assets/758baa78-d58f-4071-9ea5-a462e9dd1de6)

### 5. Route Tables

Created route tables (Spoke1-RT and Spoke2-RT) to force traffic from the Spoke-VNETs to go through the Azure Firewall in the Hub-VNET. This ensures that all traffic is inspected and filtered by the firewall before reaching the internet or other networks.

![image](https://github.com/user-attachments/assets/e7dfcf7f-1060-44e4-b263-89753384440b)

![image](https://github.com/user-attachments/assets/17ad32bd-500b-49c3-9d48-1fb89f3dd0b3)

### 6. Network Security Groups (NSGs)

Created NSGs for each subnet in both Spoke-VNETs. These NSGs have rules to allow or deny traffic based on source IP, destination IP, and port. This provides an additional layer of security at the subnet level.

#### NSG Rules

Configured detailed rules in the NSGs to control traffic flow within and between subnets. For example, the Spoke1-Data-NSG only allows traffic from the Spoke1-AppSubnet and management traffic from the Hub-VNET.

#### Azure Firewall Rules

Configured network and application rules in the Azure Firewall to control traffic between the VNETs and the internet. This ensures that only authorized traffic is allowed, protecting your resources from unauthorized access.

![image](https://github.com/user-attachments/assets/75057bda-d60a-4b16-9e30-c88eafa21617)

![image](https://github.com/user-attachments/assets/4235b283-677f-46b7-80d7-8e44f82794e5)

![image](https://github.com/user-attachments/assets/a4a40a52-576e-4921-905c-85ffe858f1ed)

![image](https://github.com/user-attachments/assets/d5389bff-2ab3-4dd6-9060-4b7634bbf1a0)

### 7. Management

Deployed Azure Bastion in the Hub-VNET. This provides a secure and seamless way to connect to VMs in your VNETs without needing to expose them to the internet through public IPs.

![image](https://github.com/user-attachments/assets/177222af-55d1-4e75-bfab-587553120685)
