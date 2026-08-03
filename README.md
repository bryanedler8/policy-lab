# Cloud Governance Gone Rogue – Azure Policy Lab

##  Lab Summary
This lab demonstrates the implementation of Azure Policy to enforce cloud governance and compliance for MapleTech Solutions, a Canadian cloud-native company. The goal was to bring order to cloud resource deployments by enforcing region restrictions, mandatory tagging, and blocking public IP addresses.

##  Lab Objectives
1. Create three custom Azure Policies
2. Group policies into an initiative (MapleTech Secure Foundation)
3. Assign the initiative to a resource group
4. Test policy enforcement with sample deployments

##  Policies Created

### 1. Only-CanadaCentral (Region Lockdown)
- **Effect**: Deny
- **Purpose**: Restricts all resource deployments to the Canada Central region only.
- **Logic**: Denies any resource where the location is not in the allowed locations parameter (`canadacentral`).

### 2. Require-ProjectName-Tag (Mandatory Tagging)
- **Effect**: Deny
- **Purpose**: Ensures all resources have a `ProjectName` tag.
- **Logic**: Denies any resource where the `ProjectName` tag does not exist.

### 3. Deny-Public-IP (Block Public IPs)
- **Effect**: Deny
- **Purpose**: Prevents creation of Public IP address resources.
- **Logic**: Denies any resource of type `Microsoft.Network/publicIPAddresses`.

##  Test Results

| Test Case | Expected Outcome | Actual Result |
|-----------|------------------|---------------|
| Deploy VM in East US | ❌ Denied | ✅ Denied (Region policy) |
| Deploy Storage Account without ProjectName tag | ❌ Denied | ✅ Denied (Tag policy) |
| Create a Public IP | ❌ Denied | ✅ Denied (Public IP policy) |
| Deploy VM in Canada Central with ProjectName tag | ✅ Allowed | ✅ Allowed |

##  Screenshots
All screenshots are available in the `screenshots/` folder.

##  Video Demo
[Video Demo Link](https://youtu.be/XCIjXYmLiAM)

##  Challenges and Lessons Learned

### Challenges
1. **Tag Propagation**: Getting tags to apply to all associated resources (VNet, NIC, Disk) during VM creation was challenging. Tags needed to be applied at the resource level, not just the VM level.

2. **Subnet Tagging**: Azure CLI does not support direct tag application to subnets through the `az network vnet subnet update` command. The workaround was to use the `az resource tag` command or the Azure Portal.

3. **Image Specification**: The correct URN format for Ubuntu images (`Canonical:UbuntuServer:24.04-LTS:latest`) was initially unclear. Using the `Ubuntu2204` alias resolved the issue.

4. **Policy Propagation**: Policy assignments take 5-15 minutes to become active, requiring patience during testing.

### Lessons Learned
- **Initiative vs. Individual Policies**: Grouping policies into an initiative simplifies management and assignment.
- **Scope Matters**: Policies only affect resources within their assigned scope (resource group, subscription, management group).
- **Tags Must Be Explicit**: Tags are not inherited from parent resources; they must be applied directly to each resource.
- **CLI is Powerful but Sometimes Tricky**: The Azure CLI provides great automation capabilities but requires precise syntax.

##  How to Reproduce
1. Create the three custom policies using the JSON definitions in `policy-definitions/`.
2. Create the initiative `MapleTech-Secure-Foundation` and add all three policies.
3. Assign the initiative to a resource group.
4. Test with the four scenarios described above.

##  References
- [Azure Policy Documentation](https://docs.microsoft.com/en-us/azure/governance/policy/)
- [Azure Policy JSON Structure](https://docs.microsoft.com/en-us/azure/governance/policy/concepts/definition-structure)
