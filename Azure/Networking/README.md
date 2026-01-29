### Azure Networking
|What is an Azure Virtual Network (VNet)?|
|-|
|An Azure virtual network (VNet) provides logical isolation for Azure cloud resources.|
|VNets enable you to provision and manage private networks, including VPN connectivity, within Azure.|
|Each VNet is assigned its own Classless Inter-Domain Routing (CIDR) address space and can be connected to other VNets or on-premises networks.|
|VNets can be linked with on-premises IT infrastructure to create hybrid or cross-premises solutions, provided the address spaces do not overlap.|
|You have full control over DNS server configuration and can segment a VNet into multiple subnets for better organization and security.|

### What is a Subnet? (Simple Table)
| Term     | Simple Meaning            | Real-World Example         |
| -------- | ------------------------- | -------------------------- |
| VNet     | Big private network       | Office building            |
| Subnet   | Smaller part of the VNet  | Floor in the building      |
| IP range | Addresses used in subnet  | Room numbers on a floor    |
| Resource | VM, service, etc.         | People in rooms            |
| NSG      | Security rules for subnet | Security guard for a floor |

### Subnet in Azure (Example)
| Item       | Example Value | What It Means              |
| ---------- | ------------- | -------------------------- |
| VNet       | 10.0.0.0/16   | Entire network             |
| Web Subnet | 10.0.1.0/24   | Web servers live here      |
| App Subnet | 10.0.2.0/24   | App servers live here      |
| DB Subnet  | 10.0.3.0/24   | Database servers live here |

### Why Subnets Are Used
| Reason          | Simple Explanation                  |
| --------------- | ----------------------------------- |
| Security        | Different rules for different tiers |
| Organization    | Keeps things clean and separated    |
| Traffic control | Decide who can talk to whom         |
| Scalability     | Easy to grow each tier              |

### 

### NSG (Network Security Group) vs ASG (Application Security Group)
| Feature           | NSG               | ASG               |
| ----------------- | ----------------- | ----------------- |
| Purpose           | Traffic filtering | Resource grouping |
| Acts as firewall  | ✅ Yes            | ❌ No             |
| Uses IP addresses | ✅ Yes            | ❌ No             |
| Simplifies rules  | ❌                | ✅                |
| Applied to        | Subnet / NIC      | NIC               |
| Can exist alone   | ✅                | ❌ (needs NSG)    |

### Here’s a real-world Azure 3-tier setup in table form.
#### Architecture Overview
| Tier | Purpose           | Subnet     | ASG     |
| ---- | ----------------- | ---------- | ------- |
| Web  | Public web access | web-subnet | asg-web |
| App  | Business logic    | app-subnet | asg-app |
| DB   | Database          | db-subnet  | asg-db  |

#### Allowed Traffic Flow (High-Level)
| From     | To  | Port     | Allowed?  |
| -------- | --- | -------- | --------- |
| Internet | Web | 80 / 443 | ✅        |
| Web      | App | 8080     | ✅        |
| App      | DB  | 1433     | ✅        |
| Internet | App | Any      | ❌        |
| Internet | DB  | Any      | ❌        |
| Web      | DB  | Any      | ❌        |

#### NSG Rules – Web Tier
| Direction | Source   | Destination | Port      | Action |
| --------- | -------- | ----------- | --------- | ------ |
| Inbound   | Internet | asg-web     | 80 / 443  | Allow  |
| Inbound   | Bastion  | asg-web     | 22 / 3389 | Allow  |
| Inbound   | Any      | Any         | Any       | Deny   |

#### NSG Rules – App Tier
| Direction | Source      | Destination | Port      | Action |
| --------- | ----------- | ----------- | --------- | ------ |
| Inbound   | **asg-web** | asg-app     | 8080      | Allow  |
| Inbound   | Bastion     | asg-app     | 22 / 3389 | Allow  |
| Inbound   | Any         | Any         | Any       | Deny   |

#### NSG Rules – DB Tier
| Direction | Source      | Destination | Port      | Action |
| --------- | ----------- | ----------- | --------- | ------ |
| Inbound   | **asg-app** | asg-db      | 1433      | Allow  |
| Inbound   | Bastion     | asg-db      | 22 / 3389 | Allow  |
| Inbound   | Any         | Any         | Any       | Deny   |

#### Why This Works (One Line Each)
| Feature         | Why It’s Good             |
| --------------- | ------------------------- |
| ASGs            | No IP addresses in rules  |
| NSGs per subnet | Clear separation of tiers |
| Explicit Deny   | Zero trust between tiers  |
| Bastion         | No public SSH/RDP         |

### Subnet vs NSG vs ASG (Single Comparison Table)
| Feature                  | **Subnet**      | **NSG (Network Security Group)** | **ASG (Application Security Group)** |
| ------------------------ | --------------- | -------------------------------- | ------------------------------------ |
| What it is               | Network segment | Firewall rules                   | Logical group/label                  |
| Main purpose             | Group resources | Allow / deny traffic             | Group VMs by role                    |
| Does it control traffic? | ❌ No           | ✅ Yes                           | ❌ No                                |
| Uses IP addresses        | ✅ Yes          | ✅ Yes                           | ❌ No                                |
| Works without others     | ✅ Yes          | ❌ No                            | ❌ No                                |
| Applied to               | VNet            | Subnet or NIC                    | NIC (VM)                             |
| Simplifies security      | ❌              | ❌                               | ✅                                   |
| Example                  | web-subnet      | Allow 443 from Internet          | asg-web                              |
| Real-world analogy       | Building floor  | Security guard                   | Team badge                           |

